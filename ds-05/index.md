# [자료구조] 해시 테이블


선형적인 자료구조 해시 테이블에 대해 정리

<!--more-->

## 해시 테이블 (Hash Table)
- 키를 값에 매핑할 수 있는 구조인 연관 배열 추상 자료형을 구현하는 자료구조
- 대부분 연산의 시간 복잡도가 $O(1)$
- Python에서 해시 테이블로 구현된 자료형 : dictionary

## 로드 팩터 (Load Factor)
- 해시 테이블에 저장된 데이터 개수 $n$을 버킷의 개수 $k$로 나눈 것

## 해시 함수(Hash Function)
- 해시함수
  - 임의 크기 데이터를 고정 크기 값으로 매핑하는데 사용하는 함수
- 해시함수를 만드는 방법은 다양하다.
- 좋은 해시함수?
  - less collision
  - fast computation

## 충돌 (Collision)
- 충돌이 일어나는 경우 이를 방지하기 위한 방법이 필요하다.
  - 체이닝 (Chaining), 오픈 어드레싱 (Open addressing)
  - Python은 Open addressing 방법을 사용
- Chaining
  - 충돌이 발생하면 연결리스트로 연결한다.
  - 충돌이 많지 않으면 대부분의 탐색은 $O(1)$ 최악은 $O(n)$
- Open addressing
  - 충돌이 일어나면 그 뒤의 가장 가까운 다음 빈 위치를 탐사 (linear probing 방법)
  - linear probing, quadratic probing, double hashing ...
  - 연산
    - `set(key, value)`
      - 해시테이블에서 key를 찾아서 있으면 key update, 없으면 key와 value 삽입한다.
    - `search(key)`
      - 찾았는데 없으면 다음칸에 빈칸이 나올 때까지 찾는다.
    - `remove(key)`
      - 충돌이 일어나서 이어지는 경우 중간에 빈칸이 없도록 해야한다.
    - 평균적으로 50% 이상 빈슬롯이면 위의 연산 모두 $O(1)$이다.
  - 성능평가
    - cluster size : 해시테이블에서 뭉쳐진 cluster가 최소
    - 충돌비율 등등

## Python 예시 
- [Link](https://github.com/minsoo9506/computer-science-study/tree/master/%5BData%20Structure%5D%20python%20algorithm%20interview)
- 해시맵 디자인
  - chaining 방식
  - 직접 해시함수를 구현
  - `collections.defaultdict` 사용
- 보석과 돌
  - A문자열이 B문자열에 얼마나 등장하는지
- 중복 문자 없는 가장 긴 부분 문자열
- 상위 K빈도 요소
  - Python에서 `zip`, `*` 의 사용법
  - `zip`은 제너레이터를 리턴
  - `zip`은 2개 이상의 시퀀스를 짧은 길이를 기준으로 일대일 대응하는 새로운 튜플 시쿼스를 만든다.

## Reference
- 파이썬 알고리즘 인터뷰 (박상길 지음)
- [신찬수, 한국외대, 컴퓨터전자시스템공학부, 자료구조 2020-1](https://www.youtube.com/watch?v=Bzmepm6pYQI&list=PLsMufJgu5933ZkBCHS7bQTx0bncjwi4PK&index=17&t=2s)
