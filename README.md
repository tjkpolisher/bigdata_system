# bigdata_system
리눅스, 도커, 하둡, 하이브 기반 실습 환경 구축 및 DB 이용을 다룬 리포지터리입니다.
# 사용 방법
## 폴더 설명
docker_file 폴더에는 실습에 사용한 각종 도커파일이 수록되어 있습니다. workspace 파일에는 하이브 등을 파이썬과 연계하여 작업한 각종 파일들이 jupyter notebook 등의 확장자로 저장되어 있습니다.

## 도커 환경 빌드 방법
workspace 폴더의 파일을 실행하기에 앞서 먼저 docker_file 폴더에서 `hadoop_hive_dockerfile2.txt`를 이용해 빌드 및 하이브 실행을 진행합니다.  
```
docker build -t hadoop_hive_env02 . -f hadoop_hive_dockerfile2.txt
docker run --hostname=localhost --privileged=true  -it -p 10000:10000 -p 8088:8088 -p 8080:8080 -p 9870:9870 -p 8042:8042 hadoop_hive_env02 /bin/bash
```

본 도커파일에는 한국어 설정이 되어 있지 않기 때문에 실행이 되었다면 사전에 아래 명령어를 이용해 한글 설정을 해주셔야 합니다.
```
localedef -f UTF-8 -i ko_KR ko_KR.UTF-8
export LC_ALL=ko_KR.UTF-8
LC_ALL=ko_KR.UTF-8 bash
```
설정이 끝났다면 아래 명령어로 하이브를 백그라운드 실행시킵니다. 이 명령어로 실행시키는 파일은 도커파일로 빌드하는 과정에서 다운받아지는 파일입니다.
`./hadoop_hive_start.sh`  
## 하이브 테이블 생성
실습을 위해 하이브 환경에서 FINANCE_Y 테이블을 생성합니다. 도커 리눅스 상에서 다음과 같이 입력합니다.
```
cd /data
hdfs dfs -mkdir /stock_finance
hdfs dfs -put finance_y.csv /stock_finance
hive
```
하이브에 접속되었다면 다음과 같은 테이블을 생성합니다.
```SQL
CREATE EXTERNAL TABLE FINANCE_Y (
  YY STRING,
  STK_CD STRING,
  FIN_ITM_NM STRING,
  FIN_ITM_VAL decimal(18,3),
  DAT_SOU STRING
) 
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION "/stock_finance";
```
제대로 생성되었는지 확인해보고 싶으시다면, 아래 쿼리를 추가로 입력해보세요. 삼성전자(종목코드 005930)의 재무 정보를 조회하는 쿼리입니다.
```SQL
SELECT *
FROM FINANCE_Y
WHERE STK_CD = '005930'
```
## 아나콘다 가상환경 설정
workspace 폴더의 requirements.txt를 이용해 환경을 생성해주세요. 실습 당시에는 `bigdata_env`라는 이름으로 가상환경을 생성했습니다. 이후의 workspace 내의 파일에서 실습하는 환경들은 모두 이 가상환경 하에서 실행되었음을 알립니다.
추가적으로, 컴퓨터에 visual studio 또는 이에 준하는 C++ 개발 도구가 설치되어 있지 않은 경우 [이 링크](https://visualstudio.microsoft.com/ko/visual-cpp-build-tools/)에서 visual studio build tool을 다운로드받은 후, C++를 사용한 데스크톱 개발을 선택하고 설치해주세요.
정상적으로 설치가 되었다면, 시작 버튼을 누르고 컴퓨터를 재부팅한 뒤에 실습을 진행하시면 됩니다.
![image](https://github.com/tjkpolisher/bigdata_system/assets/80250150/b6efc316-cc69-4563-8aae-ad15aa5fca9d)
![image](https://github.com/tjkpolisher/bigdata_system/assets/80250150/5ba78ec7-69f1-42ff-82ba-f01845937455)
