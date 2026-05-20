# Results

## 1. 실험 요약
- 저장소: bench-blackhole-render-shootout
- 커밋 해시: 7d8a355
- 실험 일시: 2026-05-20T15:36:17.300Z -> 2026-05-20T15:36:26.785Z
- 담당자: ai-webgpu-lab
- 실험 유형: `benchmark`
- 상태: `success`

## 2. 질문
- 같은 blackhole lensing fixture에서 renderer profile별 frame pacing과 품질 score를 비교할 수 있는가
- WebGPU primary와 fallback primary 실행 모드가 같은 benchmark protocol과 결과 스키마로 기록되는가
- 실제 three.js/raw WebGPU/WebGL renderer 교체 전 winner selection과 ray step/TAA 기준을 고정할 수 있는가

## 3. 실행 환경
### 브라우저
- 이름: Chrome
- 버전: 147.0.7727.15

### 운영체제
- OS: Linux
- 버전: unknown

### 디바이스
- 장치명: Linux x86_64
- device class: `desktop-high`
- CPU: 16 threads
- 메모리: 32 GB
- 전원 상태: `unknown`

### GPU / 실행 모드
- adapter: navigator.gpu readiness path, webgl-fallback
- backend: `webgpu, webgl`
- fallback triggered: `false, true`
- worker mode: `main`
- cache state: `warm`
- required features: ["shader-f16","timestamp-query"], []
- limits snapshot: {"maxTextureDimension2D":8192,"maxBindGroups":4}, {}

## 4. 워크로드 정의
- 시나리오 이름: Blackhole Render Shootout / WebGPU, Blackhole Render Shootout / Fallback
- 입력 프로필: single-lensing-fixture-three-renderer-candidates
- 데이터 크기: winner=raw-webgpu-raymarch; profiles=3; three-tsl-singularity:fps=59.75,p95=17.86,score=0.7848,raySteps=96,taa=true | raw-webgpu-raymarch:fps=69.31,p95=15.56,score=0.8439,raySteps=128,taa=false | webgl-postprocess-fallback:fps=50.8,p95=20.81,score=0.6715,raySteps=64,taa=true; automation=playwright-chromium, winner=webgl-postprocess-fallback; profiles=3; three-tsl-singularity:fps=39.16,p95=26.66,score=0.6011,raySteps=96,taa=true | raw-webgpu-raymarch:fps=31.52,p95=32.86,score=0.4996,raySteps=128,taa=false | webgl-postprocess-fallback:fps=43.7,p95=24.01,score=0.6071,raySteps=64,taa=true; automation=playwright-chromium, winner=raw-webgpu-raymarch; profiles=3; three-tsl-singularity:fps=59.75,p95=17.86,score=0.7848,raySteps=96,taa=true | raw-webgpu-raymarch:fps=69.31,p95=15.56,score=0.8439,raySteps=128,taa=false | webgl-postprocess-fallback:fps=50.8,p95=20.81,score=0.6715,raySteps=64,taa=true; realAdapter=fallback(Benchmark.Suite not available); automation=playwright-chromium
- dataset: -
- model_id 또는 renderer: raw-webgpu-raymarch, webgl-postprocess-fallback
- 양자화/정밀도: -
- resolution: 960x540
- context_tokens: -
- output_tokens: -

## 5. 측정 지표
### 공통
- time_to_interactive_ms: 1006.1 ~ 1509.4 ms
- init_ms: 35 ~ 52 ms
- success_rate: 1
- peak_memory_note: 32 GB reported by browser
- error_type: -

### Graphics / Blackhole
- avg_fps: 43.7 ~ 69.31
- p95_frametime_ms: 15.56 ~ 24.01 ms
- scene_load_ms: 35 ~ 52 ms
- ray_steps: 64 ~ 128
- taa states: false, true
- fallback states: false, true
- backends: webgpu, webgl

## 6. 결과 표
| Run | Scenario | Backend | Cache | Mean | P95 | Notes |
|---|---|---:|---:|---:|---:|---|
| 1 | Blackhole Render Shootout / WebGPU | webgpu | warm | 69.31 | 15.56 | scene_load=35 ms, fallback=false |
| 2 | Blackhole Render Shootout / Fallback | webgl | warm | 43.7 | 24.01 | scene_load=52 ms, fallback=true |
| 3 | Blackhole Render Shootout / WebGPU | webgpu | warm | 69.31 | 15.56 | scene_load=35 ms, fallback=false |

## 7. 관찰
- blackhole renderer shootout은 backend=webgpu, fallback_triggered=false로 기록됐다.
- 최고 avg_fps는 Blackhole Render Shootout / WebGPU의 69.31였고 winner=raw-webgpu-raymarch였다.
- renderer metadata는 ray_steps=128, taa_enabled=false, resolution_scale=0.74로 남았다.
- playwright-chromium로 수집된 automation baseline이며 headless=true, browser=Chromium 147.0.7727.15.
- 실제 runtime/model/renderer 교체 전 deterministic harness 결과이므로, 절대 성능보다 보고 경로와 재현성 확인에 우선 의미가 있다.

## 8. WebGPU vs Fallback
- blackhole renderer winner: webgpu=raw-webgpu-raymarch, fallback=webgl-postprocess-fallback
- avg_fps: webgpu=69.31, fallback=43.7, delta=+25.61
- p95_frametime_ms: webgpu=15.56 ms, fallback=24.01 ms, delta=-8.45 ms
- ray_steps: webgpu=128, fallback=64

## 9. Real Adapter vs Deterministic
- adapter: real=bench-blackhole-blackhole-render-shootout-214, deterministic=deterministic-renderer-shootout
- adapter_run: real=connected, deterministic=deterministic
- success_rate: real=1, deterministic=1

## 10. 결론
- blackhole renderer shootout이 WebGPU primary와 fallback primary pair를 raw JSON과 RESULTS.md 양쪽에 남기게 됐다.
- 다음 단계는 deterministic renderer profiles를 실제 three.js/TSL, raw WebGPU, WebGL/Wasm implementation으로 교체하되 winner selection과 graphics metric 구조를 유지하는 것이다.
- 이후 `bench-renderer-shootout`과 `app-blackhole-observatory`의 renderer 선택 기준으로 재사용할 수 있다.

## 11. 첨부
- 스크린샷: ./reports/screenshots/01-blackhole-render-webgpu.png, ./reports/screenshots/02-blackhole-render-fallback.png, ./reports/screenshots/10-blackhole-render-shootout-real-blackhole-bench.png
- 로그 파일: ./reports/logs/01-blackhole-render-webgpu.log, ./reports/logs/02-blackhole-render-fallback.log, ./reports/logs/10-blackhole-render-shootout-real-blackhole-bench.log
- raw json: ./reports/raw/01-blackhole-render-webgpu.json, ./reports/raw/02-blackhole-render-fallback.json, ./reports/raw/10-blackhole-render-shootout-real-blackhole-bench.json
- 배포 URL: https://ai-webgpu-lab.github.io/bench-blackhole-render-shootout/
- 관련 이슈/PR: -
