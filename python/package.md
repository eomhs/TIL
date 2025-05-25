## Packaging
- hatch 사용 기준 pyproject.toml
```toml
...

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.hatch.build.targets.sdist]
# 여기 채우기

[tool.hatch.build.targets.wheel]
# 여기 채우기

...
```
- 이렇게 하면 build 해서 .whl 또는 .tar.gz 파일 생성
- pip/uv가 .whl을 풀어서 venv/site-packages에 코드를 복사함
- import 시 이러한 venv/site-packages에서 .py 파일들을 불러옴
