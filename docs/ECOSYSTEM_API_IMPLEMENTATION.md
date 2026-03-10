# 🔧 AgentStack Ecosystem API - Детальная реализация

**См. также:** [MCP и экосистема — индекс](MCP_AND_ECOSYSTEM.md), [примеры комплексных проектов](examples/mcp_complex_projects.md).

## 📋 Технические детали

### 1. Структура endpoints

#### Базовая структура файлов:
```
agentstack-core/
├── endpoints/
│   └── ecosystem_endpoints.py      # Новый файл
├── services/
│   └── ecosystem_api_service.py     # Новый сервис (опционально)
└── core_app.py                      # Регистрация router
```

---

## 🏗️ Реализация Endpoints

### 1.1. Data API для проектов

```python
# agentstack-core/endpoints/ecosystem_endpoints.py

from fastapi import APIRouter, HTTPException, Depends, Query, Path, Body
from typing import Dict, Any, Optional
from shared.middleware.session_auth import require_authentication
from shared.dna import DNARepository, DNAFilter

router = APIRouter(prefix="/api/v1/ecosystem", tags=["Ecosystem API"])

@router.get("/data/projects/{project_id}")
async def get_project_data(
    project_id: int = Path(..., description="Project ID"),
    path: Optional[str] = Query(None, description="JSON path to specific field"),
    current_user: Dict[str, Any] = Depends(require_authentication)
):
    """
    Get project data from data_projects_project
    
    Examples:
    - GET /api/v1/ecosystem/data/projects/1025
    - GET /api/v1/ecosystem/data/projects/1025?path=config.rules
    """
    try:
        # Validate project access
        user_project_id = current_user.get('project_id')
        if user_project_id != project_id:
            raise HTTPException(status_code=403, detail="Access denied")
        
        # Get project data
        repo = DNARepository('data_projects_project')
        filter = DNAFilter(project_id=project_id)
        projects = await repo.find(filters=[filter], limit=1)
        
        if not projects:
            raise HTTPException(status_code=404, detail="Project not found")
        
        project = projects[0]
        data = project.data or {}
        
        # If path specified, extract nested value
        if path:
            from shared.utils.json_path import get_json_path
            value = get_json_path(data, path)
            return {"value": value, "path": path}
        
        return {
            "project_id": project_id,
            "data": data,
            "uuid": str(project.uuid),
            "created_at": project.created_at.isoformat() if project.created_at else None,
            "updated_at": project.updated_at.isoformat() if project.updated_at else None
        }
    
    except HTTPException:
        raise
    except Exception as e:
        logger.error(f"Error getting project data: {e}")
        raise HTTPException(status_code=500, detail=str(e))


@router.post("/data/projects/{project_id}")
async def create_project_data(
    project_id: int = Path(..., description="Project ID"),
    data: Dict[str, Any] = Body(..., description="Project data"),
    current_user: Dict[str, Any] = Depends(require_authentication)
):
    """
    Create or update project data
    
    Note: If project exists, this will merge data
    """
    try:
        user_project_id = current_user.get('project_id')
        user_id = current_user.get('user_id')
        
        if user_project_id != project_id:
            raise HTTPException(status_code=403, detail="Access denied")
        
        repo = DNARepository('data_projects_project')
        filter = DNAFilter(project_id=project_id)
        projects = await repo.find(filters=[filter], limit=1)
        
        if projects:
            # Update existing
            project = projects[0]
            existing_data = project.data or {}
            merged_data = {**existing_data, **data}
            
            await repo.update(
                entity_id=project.id,
                data=merged_data,
                user_id=user_id
            )
            return {"message": "Project data updated", "project_id": project_id}
        else:
            # Create new
            entity = await repo.create(
                project_id=project_id,
                user_id=user_id,
                data=data
            )
            return {
                "message": "Project data created",
                "project_id": project_id,
                "uuid": str(entity.uuid)
            }
    
    except HTTPException:
        raise
    except Exception as e:
        logger.error(f"Error creating project data: {e}")
        raise HTTPException(status_code=500, detail=str(e))


@router.patch("/data/projects/{project_id}")
async def update_project_data(
    project_id: int = Path(..., description="Project ID"),
    path: str = Query(..., description="JSON path to update"),
    value: Any = Body(..., description="Value to set"),
    current_user: Dict[str, Any] = Depends(require_authentication)
):
    """
    Update specific field in project data using JSON path
    
    Example:
    PATCH /api/v1/ecosystem/data/projects/1025?path=config.rules
    Body: {...}
    """
    try:
        user_project_id = current_user.get('project_id')
        user_id = current_user.get('user_id')
        
        if user_project_id != project_id:
            raise HTTPException(status_code=403, detail="Access denied")
        
        repo = DNARepository('data_projects_project')
        filter = DNAFilter(project_id=project_id)
        projects = await repo.find(filters=[filter], limit=1)
        
        if not projects:
            raise HTTPException(status_code=404, detail="Project not found")
        
        project = projects[0]
        data = project.data or {}
        
        # Update nested path
        from shared.utils.json_path import set_json_path
        updated_data = set_json_path(data, path, value)
        
        await repo.update(
            entity_id=project.id,
            data=updated_data,
            user_id=user_id
        )
        
        return {
            "message": "Field updated",
            "path": path,
            "project_id": project_id
        }
    
    except HTTPException:
        raise
    except Exception as e:
        logger.error(f"Error updating project data: {e}")
        raise HTTPException(status_code=500, detail=str(e))
```

---

### 1.2. Data API для пользователей

```python
@router.get("/data/users/{user_id}")
async def get_user_data(
    user_id: int = Path(..., description="User ID"),
    project_id: int = Query(..., description="Project ID"),
    path: Optional[str] = Query(None, description="JSON path to specific field"),
    current_user: Dict[str, Any] = Depends(require_authentication)
):
    """
    Get user data from data_projects_user
    
    Examples:
    - GET /api/v1/ecosystem/data/users/123?project_id=1025
    - GET /api/v1/ecosystem/data/users/123?project_id=1025&path=profile.level
    """
    try:
        user_project_id = current_user.get('project_id')
        if user_project_id != project_id:
            raise HTTPException(status_code=403, detail="Access denied")
        
        repo = DNARepository('data_projects_user')
        
        # Find project entity first to get its database ID
        project_repo = DNARepository('data_projects_project')
        project_filter = DNAFilter(project_id=project_id)
        projects = await project_repo.find(filters=[project_filter], limit=1)
        
        if not projects:
            raise HTTPException(status_code=404, detail="Project not found")
        
        project_db_id = projects[0].id
        
        # Find user in project
        filter = DNAFilter(
            project_id=project_db_id,  # Use database ID, not logical project_id
            user_id=user_id
        )
        users = await repo.find(filters=[filter], limit=1)
        
        if not users:
            raise HTTPException(status_code=404, detail="User not found in project")
        
        user = users[0]
        data = user.data or {}
        
        if path:
            from shared.utils.json_path import get_json_path
            value = get_json_path(data, path)
            return {"value": value, "path": path}
        
        return {
            "user_id": user_id,
            "project_id": project_id,
            "data": data,
            "uuid": str(user.uuid),
            "created_at": user.created_at.isoformat() if user.created_at else None,
            "updated_at": user.updated_at.isoformat() if user.updated_at else None
        }
    
    except HTTPException:
        raise
    except Exception as e:
        logger.error(f"Error getting user data: {e}")
        raise HTTPException(status_code=500, detail=str(e))


@router.post("/data/users")
async def create_user_data(
    project_id: int = Query(..., description="Project ID"),
    user_id: int = Query(..., description="User ID"),
    data: Dict[str, Any] = Body(..., description="User data"),
    current_user: Dict[str, Any] = Depends(require_authentication)
):
    """
    Create or update user data in project
    """
    try:
        user_project_id = current_user.get('project_id')
        current_user_id = current_user.get('user_id')
        
        if user_project_id != project_id:
            raise HTTPException(status_code=403, detail="Access denied")
        
        # Get project database ID
        project_repo = DNARepository('data_projects_project')
        project_filter = DNAFilter(project_id=project_id)
        projects = await project_repo.find(filters=[project_filter], limit=1)
        
        if not projects:
            raise HTTPException(status_code=404, detail="Project not found")
        
        project_db_id = projects[0].id
        
        # Check if user exists
        repo = DNARepository('data_projects_user')
        filter = DNAFilter(project_id=project_db_id, user_id=user_id)
        users = await repo.find(filters=[filter], limit=1)
        
        if users:
            # Update existing
            user = users[0]
            existing_data = user.data or {}
            merged_data = {**existing_data, **data}
            
            await repo.update(
                entity_id=user.id,
                data=merged_data,
                user_id=current_user_id
            )
            return {"message": "User data updated", "user_id": user_id, "project_id": project_id}
        else:
            # Create new
            entity = await repo.create(
                project_id=project_db_id,  # Use database ID
                user_id=user_id,
                data=data
            )
            return {
                "message": "User data created",
                "user_id": user_id,
                "project_id": project_id,
                "uuid": str(entity.uuid)
            }
    
    except HTTPException:
        raise
    except Exception as e:
        logger.error(f"Error creating user data: {e}")
        raise HTTPException(status_code=500, detail=str(e))
```

---

### 1.3. Rules Engine API

```python
from agentstack_core.services.rules_engine_service import rules_engine_service

@router.get("/rules")
async def list_rules(
    project_id: int = Query(..., description="Project ID"),
    enabled: Optional[bool] = Query(None, description="Filter by enabled status"),
    current_user: Dict[str, Any] = Depends(require_authentication)
):
    """
    List all rules for a project
    """
    try:
        user_project_id = current_user.get('project_id')
        if user_project_id != project_id:
            raise HTTPException(status_code=403, detail="Access denied")
        
        rules = await rules_engine_service.list_rules(project_id=project_id)
        
        # Filter by enabled if specified
        if enabled is not None:
            rules = [r for r in rules if r.get('config', {}).get('enabled') == enabled]
        
        return {
            "project_id": project_id,
            "rules": rules,
            "count": len(rules)
        }
    
    except HTTPException:
        raise
    except Exception as e:
        logger.error(f"Error listing rules: {e}")
        raise HTTPException(status_code=500, detail=str(e))


@router.post("/rules/create")
async def create_rule(
    project_id: int = Query(..., description="Project ID"),
    name: str = Body(..., description="Rule name"),
    description: str = Body("", description="Rule description"),
    json_rules: Dict[str, Any] = Body(..., description="Rule definition (when/do)"),
    enabled: bool = Body(True, description="Whether rule is enabled"),
    priority: int = Body(5, description="Rule priority (1-10)"),
    current_user: Dict[str, Any] = Depends(require_authentication)
):
    """
    Create a new rule
    """
    try:
        user_project_id = current_user.get('project_id')
        user_id = current_user.get('user_id')
        
        if user_project_id != project_id:
            raise HTTPException(status_code=403, detail="Access denied")
        
        rule = await rules_engine_service.create_rule(
            project_id=project_id,
            user_id=user_id,
            name=name,
            description=description,
            json_rules=json_rules,
            enabled=enabled,
            priority=priority
        )
        
        return {
            "message": "Rule created",
            "rule": rule
        }
    
    except HTTPException:
        raise
    except Exception as e:
        logger.error(f"Error creating rule: {e}")
        raise HTTPException(status_code=500, detail=str(e))


@router.post("/rules/{rule_id}/execute")
async def execute_rule(
    rule_id: str = Path(..., description="Rule ID (UUID)"),
    project_id: int = Query(..., description="Project ID"),
    event_data: Dict[str, Any] = Body(..., description="Event data for rule execution"),
    context: Optional[Dict[str, Any]] = Body(None, description="Additional context"),
    current_user: Dict[str, Any] = Depends(require_authentication)
):
    """
    Execute a rule directly
    
    Note: For automatic execution, use /events/emit instead
    """
    try:
        user_project_id = current_user.get('project_id')
        user_id = current_user.get('user_id')
        
        if user_project_id != project_id:
            raise HTTPException(status_code=403, detail="Access denied")
        
        result = await rules_engine_service.execute_rule(
            rule_id=rule_id,
            project_id=project_id,
            user_id=user_id,
            context_data={**(event_data or {}), **(context or {})}
        )
        
        return {
            "rule_id": rule_id,
            "result": result
        }
    
    except HTTPException:
        raise
    except Exception as e:
        logger.error(f"Error executing rule: {e}")
        raise HTTPException(status_code=500, detail=str(e))
```

---

### 1.4. Events API

```python
from shared.event_manager import event_manager

@router.post("/events/emit")
async def emit_event(
    project_id: int = Query(..., description="Project ID"),
    event_type: str = Body(..., description="Event type"),
    event_data: Dict[str, Any] = Body(..., description="Event data"),
    context: Optional[Dict[str, Any]] = Body(None, description="Additional context"),
    current_user: Dict[str, Any] = Depends(require_authentication)
):
    """
    Emit an event that will trigger matching rules
    
    This is the recommended way to trigger rules execution.
    Rules Engine will automatically find and execute matching rules.
    """
    try:
        user_project_id = current_user.get('project_id')
        user_id = current_user.get('user_id')
        
        if user_project_id != project_id:
            raise HTTPException(status_code=403, detail="Access denied")
        
        # Emit Neural Event
        from shared.neural import NeuralEventSystem
        
        neural_event = {
            "event_type": event_type,
            "event_data": event_data,
            "context": {
                "project_id": project_id,
                "user_id": user_id,
                **(context or {})
            },
            "source": "ecosystem_api",
            "timestamp": datetime.now(timezone.utc).isoformat()
        }
        
        # Emit event (Rules Engine will handle it automatically)
        await event_manager.emit(
            event_type=event_type,
            payload=neural_event
        )
        
        return {
            "message": "Event emitted",
            "event_type": event_type,
            "project_id": project_id,
            "status": "processing"
        }
    
    except HTTPException:
        raise
    except Exception as e:
        logger.error(f"Error emitting event: {e}")
        raise HTTPException(status_code=500, detail=str(e))
```

---

## 🔐 Упрощенная аутентификация

### Создание middleware для упрощенной аутентификации

```python
# shared/middleware/ecosystem_auth.py

from fastapi import HTTPException, Request
from typing import Dict, Any, Optional
from shared.middleware.session_auth import require_authentication

async def require_ecosystem_auth(request: Request) -> Dict[str, Any]:
    """
    Simplified authentication for ecosystem API
    
    Only requires:
    - X-API-Key: Project API key
    - X-Project-ID: Project ID
    
    User ID is optional and can be extracted from API key if needed
    """
    api_key = request.headers.get("X-API-Key")
    project_id_raw = request.headers.get("X-Project-ID")
    
    if not api_key:
        raise HTTPException(status_code=401, detail="X-API-Key header required")
    
    if not project_id_raw:
        raise HTTPException(status_code=401, detail="X-Project-ID header required")
    
    try:
        project_id = int(project_id_raw)
    except ValueError:
        raise HTTPException(status_code=400, detail="Invalid X-Project-ID")
    
    # Validate API key
    from agentstack_core.api_keys_service import APIKeysService
    api_keys_service = APIKeysService()
    
    key_info = await api_keys_service.validate_api_key(api_key)
    if not key_info:
        raise HTTPException(status_code=401, detail="Invalid API key")
    
    # Verify project_id matches
    if key_info.get('project_id') != project_id:
        raise HTTPException(status_code=403, detail="API key does not match project")
    
    # Extract user_id from API key if available (optional)
    user_id = key_info.get('user_id')
    
    return {
        "project_id": project_id,
        "user_id": user_id,
        "api_key_id": key_info.get('key_id'),
        "permissions": key_info.get('permissions', {}),
        "is_ecosystem": True
    }
```

---

## 📝 Регистрация в core_app.py

```python
# agentstack-core/core_app.py

# Add import
from endpoints.ecosystem_endpoints import router as ecosystem_router

# In create_app() function:
app.include_router(ecosystem_router)
```

---

## 🧪 Примеры использования

### Python SDK пример

```python
import requests

class AgentStackEcosystemAPI:
    def __init__(self, base_url: str, api_key: str, project_id: int):
        self.base_url = base_url.rstrip('/')
        self.api_key = api_key
        self.project_id = project_id
        self.headers = {
            "X-API-Key": api_key,
            "X-Project-ID": str(project_id),
            "Content-Type": "application/json"
        }
    
    def get_project_data(self, path: str = None):
        """Get project data"""
        url = f"{self.base_url}/api/v1/ecosystem/data/projects/{self.project_id}"
        if path:
            url += f"?path={path}"
        response = requests.get(url, headers=self.headers)
        response.raise_for_status()
        return response.json()
    
    def update_project_data(self, data: dict):
        """Update project data"""
        url = f"{self.base_url}/api/v1/ecosystem/data/projects/{self.project_id}"
        response = requests.post(url, json=data, headers=self.headers)
        response.raise_for_status()
        return response.json()
    
    def get_user_data(self, user_id: int, path: str = None):
        """Get user data"""
        url = f"{self.base_url}/api/v1/ecosystem/data/users/{user_id}"
        params = {"project_id": self.project_id}
        if path:
            params["path"] = path
        response = requests.get(url, headers=self.headers, params=params)
        response.raise_for_status()
        return response.json()
    
    def emit_event(self, event_type: str, event_data: dict, context: dict = None):
        """Emit event to trigger rules"""
        url = f"{self.base_url}/api/v1/ecosystem/events/emit"
        params = {"project_id": self.project_id}
        payload = {
            "event_type": event_type,
            "event_data": event_data,
            "context": context or {}
        }
        response = requests.post(url, json=payload, headers=self.headers, params=params)
        response.raise_for_status()
        return response.json()

# Usage
api = AgentStackEcosystemAPI(
    base_url="https://agentstack.tech/api",
    api_key="your_api_key",
    project_id=1025
)

# Get project data
project_data = api.get_project_data()

# Update user progress
api.update_user_data(
    user_id=123,
    data={"profile": {"level": 10, "experience": 5000}}
)

# Emit event
api.emit_event(
    event_type="user_level_up",
    event_data={"user_id": 123, "new_level": 10}
)
```

---

## 🔍 Валидация и обработка ошибок

### JSON Path утилита

```python
# shared/utils/json_path.py

def get_json_path(data: dict, path: str) -> Any:
    """
    Get value from nested JSON using dot notation
    
    Example: get_json_path({"a": {"b": 1}}, "a.b") -> 1
    """
    keys = path.split('.')
    value = data
    for key in keys:
        if isinstance(value, dict):
            value = value.get(key)
        else:
            raise ValueError(f"Cannot access '{key}' in path '{path}'")
        if value is None:
            return None
    return value


def set_json_path(data: dict, path: str, value: Any) -> dict:
    """
    Set value in nested JSON using dot notation
    
    Example: set_json_path({"a": {}}, "a.b", 1) -> {"a": {"b": 1}}
    """
    keys = path.split('.')
    result = data.copy()
    current = result
    
    for i, key in enumerate(keys[:-1]):
        if key not in current:
            current[key] = {}
        elif not isinstance(current[key], dict):
            current[key] = {}
        current = current[key]
    
    current[keys[-1]] = value
    return result
```

---

## 📊 Rate Limiting

```python
# shared/middleware/ecosystem_rate_limit.py

from fastapi import HTTPException, Request
from typing import Dict
import time
from collections import defaultdict

class EcosystemRateLimiter:
    def __init__(self):
        self.requests = defaultdict(list)
        self.limits = {
            "default": {"requests": 100, "window": 60},  # 100 requests per minute
            "data": {"requests": 200, "window": 60},
            "events": {"requests": 50, "window": 60},
            "rules": {"requests": 20, "window": 60}
        }
    
    async def check_rate_limit(self, request: Request, endpoint_type: str = "default"):
        """Check if request is within rate limit"""
        api_key = request.headers.get("X-API-Key")
        if not api_key:
            return
        
        limit_config = self.limits.get(endpoint_type, self.limits["default"])
        now = time.time()
        window_start = now - limit_config["window"]
        
        # Clean old requests
        key_requests = self.requests[api_key]
        self.requests[api_key] = [
            req_time for req_time in key_requests
            if req_time > window_start
        ]
        
        # Check limit
        if len(self.requests[api_key]) >= limit_config["requests"]:
            raise HTTPException(
                status_code=429,
                detail=f"Rate limit exceeded: {limit_config['requests']} requests per {limit_config['window']} seconds"
            )
        
        # Record request
        self.requests[api_key].append(now)

rate_limiter = EcosystemRateLimiter()
```

---

## ✅ Следующие шаги

1. **Реализовать базовые endpoints** (Data API)
2. **Добавить Rules Engine интеграцию**
3. **Реализовать Events API**
4. **Добавить упрощенную аутентификацию**
5. **Добавить rate limiting**
6. **Создать документацию и примеры**
7. **Тестирование**

