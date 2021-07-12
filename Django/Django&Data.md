# Urls.py 

---

path ('wordCount/result',wordCount.home, name="result")

wordCount /result: 웹사이트의 로컬호스트 뒷쪽

wordCount.home : views.py 함수 실행

name="result"  : 어딘가에서 접근을 위한 namespace



wordCount.home //wordCount 페이지의 home.html로 이동

home.html에서 action="result"는 urls.py에서 result로 이름 지어진 path에 요청을 보내는 것



# Database

---

- ## Database

