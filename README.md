# GTX Compressor  （Technique preview version ）

Powered by GTXLab of Genetalks.

technique preview download URL:https://github.com/Genetalks/gtz/archive/0.2.2b_tech_preview.tar.gz

[中文说明](https://github.com/Genetalks/gtz/blob/master/README_chs.md "Markdown").

## System Overview

GTX Compressor is a fastq compressor and also can be used as a generic data compression system, developed by GTX Lab of Genetalks Cooperation, aiming at directly compressing large-scale data to the cloud (data with size up to several GB or even several terabytes, especially bioinformatics data). GTX Compressor can rapidly compress any gene sequencing files and directories with high compression rate, and generate a single compressed data files, thus facilitating the data storage, remote transmission, and verification. Different from the previous compression tools, GTX Compressor system focuses on **high compression rate, high speed, and convenient data extraction.**

GTX Compressor compresses the 33 qualities of FASTQ files (NA12878_1.fastq), with the size of approximately 200GB, to 19% of the original size, in less than 13 minutes, over the AWS R4.8xlarge machine (or the same configuration server) at a speed of more than 256MB/s. As the FASTQ data which is producted by X10 with only **7 qualities, GTX Compressor can gains 5.5% compression.**

**GTX Compressor provides "Directly compress to the cloud" function**. Out of commercial consideration, users not only need to store the massive data generated by gene sequencing locally, but also need to quickly and steadily transfer the data to the cloud. GTX Compressor system can compress the fastq files and concurrently transfer the compressed data to the Amazon AWS S3 platform or Ali cloud OSS platform, by supplying the same compression speed and compression rate with local compression. With ordinary 100Mbits Intenet line, GTX Compressor can directly compress 200GB Fastq file to the cloud in just 30 minutes.

## System highlights

GTX Compressor system features:

- **high compression ratio:** The system implements Context Model compression technology, with a variety of optimized predicting model, and balancing the system concurrent and memory resources consumption, thus achieving a high compression rate. For FASTQ files, GTX compressor gains up to 5.58% the compression rate.

- **High performance:** GTX compressor fully exploits the concurrency of the CPU, the new Haswell CPU architecture, and the computing power of the new instructions such as AVX2, BMI2, which makes GTX compressor gain high compression speed even on a common server, with the throughout of 114MB/s for the whole process of compression and transmission.

- **high-speed direct compression to the cloud:** GTX compressor support direct compression to the cloud and direct decompression from the cloud. Over a common 20-core server with 100Mbits Intenet line, GTX Compressor can derectly compress 200GB Fastq file to the cloud in only 30 minutes.

## System environment requirements
- 64-bit Linux system (CentOS 6.5 or above, or Ubuntu 12.04 or more, and with Ububtu 14.04 and above 64-bit operating system recommended)
- the host system with 4-core or more, and the minimum 8GB memory (to achieve maximum concurrency, the host system with 32-core 64GB memory is recommended, or that has the same configuration with the AWS C4.8xlarge machine)


## Installation Instruction
GTX compressor system can be directly used by unpacking, and does not rely on any other library.
The download package contains two tar.gz packages for the ubuntu version and the centos version respectly. Choose the corresponding tar.gz package, extract, and use gtz command for the extraction gtz_0.1_ubuntu_tech_preview directory or gtz_0.1_centos_tech_preview directory.

## Command line instructions

./gtz -h, and get command line help instructions.

```
USAGE: 
./gtz  [--list] [-e <string>] [-f] [--endpoint <string>] [--timeout <string>]
          [--secret-access-key <string>] [--access-key-id <string>] [-b
          <string>] [-s <string>] [-c] [-n <string>] [-l <string>] [-i]
          [-d] [--delete] [-a] [-g <number>] [-o <string>] [--] [--version]
          [-h] <file names> ...
```


General Options Instruciton:
- -h: Outputs the above command line help information
- \-\- version: Outputs the version number of the gt_compress program
- \-\- access-key-id: Specifies the cloud platform user ID
- \-\- secret-access-key: Specifies the cloud platform user key
- \-\- endpoint: Specifies the access domain name and data center of the Ali cloud OSS platform

Compression Option Description:
- -f, \-\- force
- \-\-timeout: Specifies the upload timeout threshold
- -i: Increases the index during the compression, which mainly used in the compressed file to quickly retrieve a section of the fastq file, and might reduce the compression speed
- -a: append mode, the original file will be appended to the compressed data
- -g: the speed-up compression in group, the more groups, the more need for cpu and memory, and the faster compression. If you do not specify this value, the program will automatically select the optimal value based on cpu and memory.
- -o: Specifies the compressed file name. When not specified, the default is out.gtz
- file_name: the file or directory need to be compressed. If not specified, the system will read data from the standard input.

Decompression Option Description:
- -d, \-\-decode: decompression mode, required
- \-\-list: List all compressed file names in the archive, used together with the -d parameter
- -e, \-\-extract: decompresses and extract the target files specified (The file names are separated by ":") in the compressed file. Must used together with the -d parameter
- -f, \-\-force: Forcely delete the object within the container
- \-\-timeout: Specifies the download timeout value
- -c, \-\-stdout: output to console(standard output). It can only be use for decompression.
- file_name: the file to be decompressed

### Examples:

Configure environment variables:

export access_key_id=xxxxxx

export secret_access_key=xxxxxx

export endpoint=xxxxxx   （Only set when transfering to OSS）

### Compression examples

Direct compression to Ali OSS:

	./gtz  -o oss://gtz/out.gtz   source.fastq  （or source.fastq.gz , gtz supports recompress fastq.gz file）

   	or

	zcat source.fastq.gz  |  ./gtz  -o oss://gt-compress/out.gtz

Direct compression to AWS S3

	./gtz  -o s3://gtz/out.gtz   source.fastq  （or source.fastq.gz , gtz supports recompress fastq.gz file）

	or:

    zcat source.fastq.gz  |  ./gtz  -o s3://gt-compress/out.gtz

Direct compression locally

    ./gtz  -o gtz/out.gtz   source.fastq  

    or:

	zcat source.fastq.gz  |  ./gtz  -o gtz/out.gtz


Massive small files (<500MB each) compression:

	
	To compress a large number of small files (500MB or less) to an package or transfer to the cloud, GTZ can work with tar by using pipe. It is very useful to compress and transfer amounts of small data at very fast speed. (Note: In this way, GTZ uses binary data compression algorithm, the fastq or fastq.gz files inside the tar package will not be treated specially ).
	tar -cf - ./you_dir_or_file | gtz -o /dest.gtz

	Direct compression to AWS S3 or Aliyun OSS: 
	tar -cf - ./you_dir_or_file | gtz -o s3://bucket/dest.gtz
	
	tar -cf - ./you_dir_or_file | gtz -o oss://bucket/dest.gtz

	Direct decompression：
	
	gtz -c -d /dest.gtz | tar -xf - 
	
	gtz -c -d s3://bucket/dest.gtz | tar -xf - 
	
	gtz -c -d oss://bucket/dest.gtz | tar -xf - 

	Notice: Large size files (500MB or more) or the directory full of Large size files, especially fastq or fastq.gz file or its directory, we suggest to use GTZ to directly compress and package, it will be more faster.


### Add files to the compressed package

	./gtz -a -o oss://gtz/out.gtz /A/source2.fastq # -a denotes it is the additional mode

	./gtz -a -o s3://gtz/out.gtz /A/source2.fastq # -a denotes it is the additional mode
	
	./gtz -a -o gtz /out.gtz /A/source2.fastq # -a denotes it is the additional mode


### View the files contained in the compressed gtz file

	./gtz_0.2.0_ubuntu_release/gtz --list -d oss://gtz/out.gtz

	./gtz_0.2.0_ubuntu_release/gtz --list -d s3://gtz/out.gtz

	./gtz_0.2.0_ubuntu_release/gtz --list -d gtz/out.gtz


### Decompression examples


Direct decompression from Ali OSS

	./gtz  -d oss://gtz/out.gtz

    Decompress several files separately:

    # -e denotes the target decompression files, seperated by ":"
	./gtz -e source.fastq:/A/source2.fastq -d oss://gtz/out.gtz

    Decompress the target firles to the tube:

    # -c denotes output files to the console; -e denotes the target decompression file.
    ./gtz -c -e source.fastq  -d oss://gtz/out.gtz > myfile.txt

    or

    ./gtz -c -e source.fastq  -d oss://gtz/out.gtz | gzip -c > source.gz


Direct decompression from AWS S3

	./gtz  -d s3://gtz/out.gtz

    Decompress several files separately:

    # -e denotes the target decompression files, seperated by ":"
	./gtz -e source.fastq:/A/source2.fastq -d s3://gtz/out.gtz

    Decompress the target firles to the tube:

    # -c denote output files to the console; -e denotes the target decompression file.
    ./gtz -c -e source.fastq  -d s3://gtz/out.gtz > myfile.txt
    or
    ./gtz -c -e source.fastq  -d s3://gtz/out.gtz | gzip -c > source.gz

Direct decompression locally

    ./gtz  -d ./gtz/out.gtz

    Decompress several files separately:

    # -e denotes the target decompression files, seperated by ":"
    ./gtz -e source.fastq:/A/source2.fastq -d gtz/out.gtz

    Decompress the target firles to the tube:

    # -c denote output files to the console; -e denotes the target decompression file.
    ./gtz -c -e source.fastq  -d gtz/out.gtz > myfile.txt
    or
    ./gtz -c -e source.fastq  -d gtz/out.gtz | gzip -c > myfastq.gz


## contact us

If you have any questions, feel free to contact: gen.li@genetalks.com, or commit an issus on Github.

