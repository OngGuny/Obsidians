# Batch Job 
must have business logic

변환 - 검증 - 추출 - 추출(읽음)/업데이트 - 처리/업데이트 - 산출/포멧


- _Conversion Applications:_ For each type of file supplied by or generated for an external system, a conversion application must be created to convert the transaction records supplied into a standard format required for processing. This type of batch application can partly or entirely consist of translation utility modules (see Basic Batch Services).
    
- _Validation Applications:_ A validation application ensures that all input and output records are correct and consistent. Validation is typically based on file headers and trailers, checksums and validation algorithms, and record-level cross-checks.
    
- _Extract Applications:_ An extract application reads a set of records from a database or input file, selects records based on predefined rules, and writes the records to an output file.
    
- _Extract/Update Applications:_ An extract/update applications reads records from a database or an input file and makes changes to a database or an output file, driven by the data found in each input record.
    
- _Processing and Updating Applications:_ A processing and updating application performs processing on input transactions from an extract or a validation application. The processing usually involves reading a database to obtain data required for processing, potentially updating the database and creating records for output processing.
    
- _Output/Format Applications:_ An output/format applications reads an input file, restructures data from this record according to a standard format, and produces an output file for printing or transmission to another program or system.

# Typical processing options 
for batch are (in increasing order of implementation complexity):

- Normal processing during a batch window in offline mode. : 일반 처리
    
- Concurrent batch or online processing. : 동시 배치 / 실시간 처리
    
- Parallel processing of many different batch runs or jobs at the same time : 일괄 병렬 처리
    
- Partitioning (processing of many instances of the same job at the same time):  분할
    
- A combination of the preceding options : 앞의 것들 복합 사용


분할 전략
_1. Fixed and Even Break-Up of Record Set_ : 고정된 짝수 레코드로 분할, 모든 데이터 읽어야 해서 오버헤드 클 수 있음