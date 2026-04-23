# VFX / Niagara 학습 노트

## 반투명 연기 깜박임 이슈 (2026-04-22)

### 증상
베이스컬러 기반 반투명 연기에서 조명값이 적용됐다가 무시됐다가 반복하며 깜박임

### 원인 후보
1. **TAA(Temporal Anti-Aliasing) 간섭** — 가장 흔한 원인
2. **Translucency Lighting Mode 설정** 문제
3. **파티클 정렬(Sort) 이슈** — 깊이값 비슷한 파티클들의 프레임별 정렬 순서 변화

### 해결 순서
1. 머터리얼에서 **Responsive AA** 체크 (70% 해결)
2. 안 되면 **Translucency Lighting Mode** 확인 및 변경
   - `Volumetric NonDirectional`
   - `Volumetric Directional`

---

## 한 이미터에서 메쉬별 포지션 개별 세팅 (2026-04-23)

### 조건
- GPU 이미터 가능 (`Particles.ID.Index` 사용)
- CPU도 동일하게 작동

### 1. 이미터 설정
- Sim Target: GPU or CPU
- Spawn Burst Instantaneous → Count → 메쉬 개수 (예: 4)

### 2. 파티클 스폰 모듈

**① Set MeshIndex**
- 파라미터: `int32` → MeshIndex
- 값: `Particles.ID.Index`
- 주의: `Emitter.SpawnIndex`는 GPU에서 미지원

**② Set Position (Add Vector to Position)**
- Position: `Engine.SimulationPosition`
- Vector: `Convert Position to Vector` → `Select Position from Array`
  - Array Sampling Mode: `Direct Set`
  - Direct Array Index: `MeshIndex`
  - Direct Array Mode: `Wrap`
  - Position Array: 인덱스별 XYZ 값 입력

### 3. Mesh Renderer
- Meshes 배열에 메쉬 등록
- Mesh Index Binding → `MeshIndex` 연결

### 결과
파티클 ID 순서대로 각 메쉬 + 포지션 1:1 매핑
