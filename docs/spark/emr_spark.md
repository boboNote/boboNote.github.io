
## Emr With Spark 
- Spark 워크로드 최적화 
- Spark on EMR 성능 최적화 방안 
- EMR Runtime for apache Spark 
- Apache Hudi - 레코드 레벨의 데이터 Update, delete, inssert
- Spark Job 디버그 및 모니터링을 위한 Off-cluster Spark Log 관리 
- Lake Formation 과 EMR 통합 
- Docker 환경에서 Spark 어플리케이션 배포 가능 


## 엔터프라이즈 레벨의 Hadoop 플랫폼 
- HDFS -> S3
- EMR => 엔터프라이즈 HADOOP Cluster

### EMR 노드 구성 
- 마스터 노드 
 - 클러스터 관리 
 - NameNode와 JobTracker포함 

- 코어 노드 
 - 작업 실행을 위한 Task tracker 
 - 하둡에서의 DataNode 

- 태스트 노드 
 - Task tracker만 설치 
 - 로컬 HDFS 없음 


### Stateless 클러스터 아키텍처 권장. 
**HDFS 저장소가 노드에 연결됨에 따라 재약 발생한다.**
- 클러스터 외부에 메타스토어 유지 (Glue Data Catalog 또는 RDS)
- 빠르게 시작하고 목적한 작업을 수행 
- 동시에 여러 클러스터가 S3 데이터를 활용 가능

### Spark Memory 구성 
- yarn.nodemanager.resource.memory-mb: 익스큐터 전체의 물리 메모리 영역 
- spark.executor.memory : 익스큐터가 job 실행에 사용할 수 있는 메모리 영역 
- spark.shuffle.memoryFraction : 전체 힙 영역에서 익스큐터와 RDD 데이터 저장에 사용될 비율 
- spark.storage.memoryFraction : 할당된 메모리에서 데이터 저장에 사용할 비율 
- spark.yarn.executor.MemoryOverHead : VM 관련 오버헤드를 위해 할당, 필요시 조정 가능 

### Spark 워크로드 최적화 (1/2) 
Spark 작업은 일반적으로 대량의 데이터를 Memory 기반의 리소스에서 빠르게 처리해야 하는 요구사항을 가진다. 

- 대량의 데이터 작업에서 로딩하는 데이터를 줄여주는 접근이 가장 중요 
 - 최적의 데이터 포멧 활용 = Apache Parquet compressed by Snappy(Spark 2.x의 기본값)
 - 데이터 스캔 범위를 최소화 한다. Hive Partitions, Bucketing, Push Down, Partitioning Pruning 
 - 특정 파티션에 Data Skew(편향)가 발생하면 파티션 키에 대한 고려가 필요

- 사용 가능한 클러스터의 메모리를 최대한 효율적으로 사용 
 - 작업에서 빈번하게 사용되는 데이터를 메모리에 캐쉬한다. 
 - 위크로드에 적절한 Spark 설정값을 지정해줘야 함. 
 - 작업 유형에 적합한 파티션 사이즈로 변경한다. -spark.sql.files.maxpartitionBytes

- 리소스를 가장 많이 사용하는 조인 및 셔플링 최적화 
 - 셔플링과 repartitioning은 가장 비용이 비싼 오퍼레이션이므로 최소화 되도록 Job 모니터링 
 - Spark 2.3 이후 기본 조인은 SortMerge(조인전 각 데이터셋의 정렬이 필요), Broadcast Hash조인은 대상 테이블의 사이즈가 크게 차이나는 경우 유용, 특히 10Mb 미만 테이블은 자동 Broadcast, 설정을 통해 변경 가능 spark.sql.autoBroadcastJoinThreshold 
 - 테이블 사이즈에 따라 조인 순서를 변경하여 대량 데이터 셔플링 방지 - Join Reorder 

- 클러스터 규모와 작업 유형에 따라 적절한 환경 변수 설정 
 - 익스큐터 갯수 설정 --num-executors
 - 익스큐터의 코어수 설정 --executor-cores
 - 익스큐터의 메모리 크기를 변경 --executor-memory 
```
%%congigure
{"executorMemory":"3072M" , "executorCores":4, "numExcutors":10}
```

- 스토리지 성능 최적화 
 - 데이터 활용 빈도 및 속도 요구치에 맞춰 HDFS, EMRFS(S3) 활용 선택 
 **IO Speed 'S3 < EMRFS < HDFS'**

## Spark의 주요 메모리 이슈 유형
- 자바 힙메모리 부족 오류 - Spark instance수 excutor Memory, core수등이 많은 양의 데이터를 처리할 수 있도록 설정되지 않는 경우 
OOM 발생 
- 물리 메모리 초과 - 가비지 컬렉션과 같은 시스템 작업을 수행하는 데 필요한 메모리를 spark executor 인스턴스에서 사용할 수 없는 경우 
```
Error: ExecutorLostFailure Resone : Container Killed By YARN exceeding limits. 12.4GB of 12.3GB physical memory used. 
```
- 가상메모리 초과 - 가비지 컬렉션과 같은 시스템 작업을 수행하는데 필요한 메모리를 Spark executor 인스턴스에서 사용할 수 없는 경우 
Container killed by YARN for exeeeding memory limits. 
1.1gb of 1.0Gb virtual memory used. killing containers.

- 익스큐터 메모리 초과 - Spark executor 물리적 메모리가 Yarn에서 할당한 메모리를 초과하는 경우
```
Requeired executor memory(1024+384MB) is above the max threshold(896Mb) of this cluster 
please check the values of 'yarn.scheduler.maximu,-allocation-mb'and 'yarn.nodemanager.resource.memory-mb'
```

## Spark 메모리 관련 주요 파라미터 설정 사례 
- EMR에서 Spark-defaults 값을 통해 기본값 설정이 되어 있으나 전반적으로 낮게 설정된 값으로 인해 애플리케이션이 클러스터 전체 성능을 사용하지 못하므로 추가 설정이 필요함. 

- Spark 구성 파라미터 
 - spark.executor.memory - 작업을 실행하는 각 익스큐터에 사용할 메모리의 크기입니다. 
 - spark.executor.cores - 익스큐터에 할당되는 가상 코어의 수입니다. 
 - spark.driver.memory - 드라이버에 사용할 메모리의 크기입니다. 
 - spark.driver.cores - 드라이버에 사용할 가상 코어의 수입니다. 
 - spark.executor.instances - 익스큐터의 수입니다. `spark.dynamicAllocation.enabled`가 `true`로 설정된 경우 외에는 파라미터를 설정합니다. 
 - spark.default.parallelism - 사용자가 파티션 수를 설정하지 않았을 때 join, reduceBykey 및 Paralleize와 같은 변환에 의해 반환된 RDD 파티션 수 기본값 입니다. 

## Spark 메모리 관련 주요 파라미터 설정 사례 
- r5.12xlarge(48 vCPU, 384 Gb 메모리) 마스터 1대, r5.12xlarge코어노드 19대의 EMR 클러스터로 S3에 저장된 10TB 데이터 처리 환경.
Core = 48 + (48 * 19) = 960, Memory = 384 + (383 * 19) = 7680
 - 익스큐터당 5개의 vCPU할당 spark.executor.cores = 5(vCPU)
 - 인스턴스당 익스큐터수 계산 (48-1)/5 = 9.4 => 9 
 - 인스턴스 메모리 384Gb 중 90%는 각 익스큐터에 할당 **spark.executor.memory = 버림(단인 노드 총매모리 / 단인노드 활성 익스큐터 수) * 사용비율(1=100%) = ROUND DOWN(384/9)*0.9 = 42 * 0.9 = 37** `RoundDown`을 하는 이유는 100%를 Excutor에 할당시 Os Resouce 가 문제가 발생한다.
 - 약 10%는 각 익스큐터의 Overhead에 할당 spark.yarn.executor.memoryOverhead = 42 * 0.1 = 5 
 - 드라이버 메모리는 익스큐터와 동일하게 spark.driver.memory = spark.executor.memory 
 - 전체 익스큐터 수는 spark.executor.instances = (9*19) - 1(드라이버수) = 170 
 - 병렬처리 값은 **spark.default.parallelism** = 170(익스큐터수)*5(코어수)*2 = 1,700

 ## Spark Configuration 변경 방법 
 Spark configuration을 변경하기 위한 방법 여러가지가 있으나 각각은 configu값을 적용하는 시점이 달라 1번 부터 우선순위가 가장 높게 반영된다. 
 1. SparkConf의 Set함수를 이용하여 Runtime에서 설정 값을 변경한다. 

 2. Spark-submit

 3. conf/spark-defaults.conf 파일을 직접 수정 


## Spark On EMR 성능 최적화 

### Spark on EMR 성능 최적화 기본 팁 
- 데이터의 크기와 작업의 사이즈에 적적한 메모리 할당이 가능한 instancetype을 선택(같은 vCPU를 가진 c타입 인스턴스 대비 m/r 타입 인스턴스가 두배의 메모리를 가짐)
- 최신 버젼의 Emr버젼을 사용한다.(최소 5.24.0 이상) 버전 5.28 이후에는 EMR Runtime for Apache Spark이 포함되어 있어 성능 향상 
- 추가적인 성능 최적화를 위해서 워크로드 특성에 맞는 메모리 설정 변경이 필요 

### Spark on EMR 성능 최적화 

- Dynamic Partition Pruning = 쿼리 대상 테이블을 보다 정확하게 선택하여 스토리지에서 읽고 처리하는 데이터량을 줄여 주어 시간과 리소스 절약 
 - `spark.sql.dynamicPartitionPruning.enabled` (EMR 5.24 이후 사용가능 , 5.26 이후 기본적으로 활성화)
 - where 절의 조건에 맞는 데이터만 필터링 하여 'North America'영역에 해당하는 파티션 데이터만 읽어서 처리 
 ```SQL
 Select ... 
   From ... 
 Where country = 'North America' 
 ```

- Flatening Scalar Subqueries - 다수개의 서브쿼리를 하나로 통합하여 재작성, 수행성능을 향상 
 - `spark.sql.optimizer.flattenScalarSubqueriesWithAggregates.enavled`
 - 동일한 관계를 사용하는 다수의 스칼라 쿼리를 하나의 쿼리로 통합하여 실행하여 성능을 향상 
 ```SQL
 음 
 ```

- DISTINCT BEFOR INTERSECT = Intersect 사용시 자동적으로 Left semi join 으로 변환 Distinct연산을 Intersect 하위 항목을 푸시하여 성능 향상 
 - `spark.sql.optimizer.distinctBeforeintersect.enabled`

- Bloom Filter Join - 사전에 작성된 Bloom Filter를 통해 쿼리 성능향상 

- Optimized Join Reorder - 쿼리에 적혀있는 테이블의 순서를 필터와 데이터 규모에 따라 재정렬하여 소규모를 먼저 수행 
 - `spark.sql.optimizer.size......`

- EMRFS S3 최적화된 커미터 사용 
 - Spark Properties: 'spark.sql.parquet.fs.optimized.committer.optimization-enSabled'
 - 기본적으로 S3 multipart upload 옵션이 활성화 된 상태에서 Spark SQL/DataFrames/DataSets에서 Parquet형식으로 저장 할 때 가능 

## S3를 통한 Spark성능 향상 
 - S3 select를 통해 데이터 필터링을 S3로 푸시다운 
  - spark, Presto, Hive에서 S3 Select 를 통해 대용량 데이터 필터링을 S3레벨에서 사전 처리 가능 
  - CSV, JSON, Parquet 파일 형식 지원 / 압축 파일 지원 


## Spark Emr Runtime 최적화 방법 
- Spark 작업 실행을 위한 최적의 configuration 값 세팅 
 - CPU/disk rations, driver/excutor conf, heap/GC, native overheads, instance defaults 
- 데이터 작업 플랜 생성의 최적화 
 - Dynamic partition pruning , join reordering 
- Query execution 최적화 
 - Data Pre-fetch And more 
- Job Startup 설정 셋팅 
 - Eager executor allocation,and more

