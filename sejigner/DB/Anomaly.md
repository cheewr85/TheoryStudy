# Anomaly 이상 현상
> 잘못된 테이블 설계로 이상 현상이 발생하는 것을 막기 위해 정규화가 필요하다.

## 삽입 이상 (Insertion Anomaly)
학생의 정보를 담는 기본키가 Student ID와 Course ID일 경우, Course를 수강하지 않는 학생은 Course ID 값이 Null이 되어 테이블에 삽입할 수 없다. '미수강'과 같이 불필요한 데이터를 함께 삽입해야 하는 경우
## 갱신 이상 (Update Anomaly)
학생의 전공을 컴퓨터에서 음악으로 바꿀 때, 모든 테이블에서 값이 변경되어야 하는데 일부가 누락되어 다른 값을 가질 경우
## 삭제 이상 (Deletion Anomaly)
{Student ID, Course ID, Department, Course ID, Grade} 테이블에서 Course ID를 지운다는 것이. 다른 정보까지 함께 지우는 등 원치 않는 데이터까지 손실이 되는 경우

