## pickle
- 파이썬 객체의 직렬화와 역직렬화에 쓰이는 라이브러리
- 사용방법
```python
import pickle

# 직렬화
with open("example.pkl", "wb") as f:
    pickle.dump(example, f)

# 역직렬화
with open("example.pkl", "rb") as f:
    example = pickle.load(f)
```