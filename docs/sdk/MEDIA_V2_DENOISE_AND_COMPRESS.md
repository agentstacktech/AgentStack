# SDK Media v2 — denoise & compress

## Audio (`sdk.media.audio`)

`createAudioCapture` wires a capture graph with optional **noise suppression** via WASM worklets when the browser exposes secure contexts and workers. Fallback: browser built-in noise suppression only.

## Photo (`sdk.media.photo`)

`compress` walks a multi-size ladder (**AVIF / WebP / MozJPEG**) with optional **BlurHash** placeholders for progressive UX.

## Flags & rollout

Host apps may gate features behind capability flags — treat denoise/compress as **progressive enhancement**: always ship a pass-through path.

## Messenger integration

Voice/video notes and attachment pipelines call the same primitives before upload — saving uplink and server CPU.

## Observability

Integrators consume outcomes (smaller payloads, fewer retries). Internal beacon names are **not** part of this public contract.
