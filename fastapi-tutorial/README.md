# 헷갈렸던 개념


## async 와 await

  __async__ 와 함께 정의된 함수는 비동기로 동작함

  * 비동기 함수 내에서 __await__ 가 사용되면 해당 함수를 동기 처럼 사용
    - ex ) 
    
  - [참고링크1](https://lsjsj92.tistory.com/649)
  - [참고링크2](https://teddylee777.github.io/python/python-async/)

## request로 http 통신 할 때의 async

  __asyncio__ 의 __asyncio.to_thread(requests.post, URL, DATA)__ 사용

  request의 통신은 async event loop를 block시킴
  - [참고 링크](https://superfastpython.com/python-async-requests/)
  
  따라서 비동기적으로 통신하는(thread로 동작) asyncio.to_thread 함수를 사용
