# avro2parquet - write Parquet to plain files (i.e., not Hadoop hdfs)

Based on example code snippet `ParquetReaderWriterWithAvro.java` located on github at:
 
[**MaxNevermind/Hadoop-snippets**](https://github.com/MaxNevermind/Hadoop-snippets/blob/master/src/main/java/org/maxkons/hadoop_snippets/parquet/ParquetReaderWriterWithAvro.java)
 
Original example code author: Max Konstantinov [MaxNevermind](https://github.com/MaxNevermind)

Extensively refactored by: Roger D. Voss [roger-dv](https://github.com/roger-dv), Tideworks Technology, May 2018

## IMPLEMENTATION NOTES:

- Original example wrote 2 Avro dummy test data items to a Parquet file.

- The refactored implementation uses an iteration loop to write a default of 10
Avro dummy test day items and will accept a count as passed as a command line
argument.

- The test data strings are now generated by RandomString class to a size of 64
characters.

- Still uses the original avroToParquet.avsc schema by which to describe the Avro
dummy test data.

- The most significant enhancements is where the code now calls these two methods:

`nioPathToOutputFile()` and `nioPathToInputFile()`

`nioPathToOutputFile()` accepts a Java nio `Path` to a standard file system file path
and returns an `org.apache.parquet.io.OutputFile` (which is accepted by the
`AvroParquetWriter` builder).

`nioPathToInputFile()` accepts a Java nio Path to a standard file system file path
and returns an `org.apache.parquet.io.InputFile` (which is accepted by the
`AvroParquetReader` builder).

These methods provide implementations of these two `OutputFile` and `InputFile` adaptors
that make it possible to write Avro data to Parquet formatted file residing in the
conventional file system (i.e., a plain file system instead of the Hadoop hdfs file system)
and then read it back.

Usecase: Dremio can be incrementally loaded with data provided in Parquet format files.

- It is an easy matter to adapt this approach to work with JSON input data - just
synthesize an appropriate Avro schema to describe the JSON data, put the JSON data
into an Avro `GenericData.Record` and write it out.

## NOTES ON BUILDING AND RUNNING PROGRAM:

- Build: `mvn install`

- **`HADOOP_HOME`** environment variable should be defined to prevent an exception from being
thrown - code will continue to execute properly but defining this squelches it. This is
down in the bowels of Hadoop/Parquet library implementation - not behavior from the
application code.

- **`HOME`** environment variable may defined. The program will look for logback.xml there
and will write the Parquet file it generates to there. Otherwise the program will
use the current working directory.

- In `logback.xml`, the filters on the `ConsoleAppender` and `RollingFileAppender` should be
adjusted to modify verbosity level of logging. The defaults are set to `INFO` level. The
intent is to allow, say, setting file appender to `DEBUG` while console is set to `INFO`.

- The only command line argument accepted is the specification of how many iterations
of writing Avro records; the default is 10.

- Can use the shell script `run.sh` to invoke the program from the Maven `target/` directory.

- Logging will go into a `logs/` directory as the file `avro2parquet.log`.
