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
