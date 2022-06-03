# Redis
보통 DB는 HDD나 SSD에 저장하지만 Redis는 key/value 구조를 RAM에 저장함으로써 디스크 스캐닝이 필요없어져 매우 빠름

RAM에 저장된 후 컴퓨터가 종료될 때에는 백업됨
- Snapshot : 특정 지점을 디스크에 백업
- AOF(Append Only File) : 쿼리문들을 저장해두는 파일. 서버가 셧다운되면 이 파일을 재실행

Value 자료형 5가지
- String (Text, Binary data) - 512MB까지 저장 가능
- Set (String의 집합)
- Sorted Set (정렬된 Set)
- Hash
- List(양방향 연결 리스트 형태도 가능)
