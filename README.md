# Project:  SCGI protocol for php-fpm
### A version of PHP-FPM using the SCGI protocol

## Original Source:
* https://www.php.net/distributions/php-7.4.0.tar.gz

## Description:

The FastCGI Process Manager for PHP (php-fpm) has been integrated into the core of php.  This code provides the ability to run traditional CGI programs written in PHP on a remote server.  The web server communicates with this php-fmp server via the FCGI (Fast Common Gateway Interface).  The main benefits of the php-fpm server includes:

* to off load the execution of php programs to a different deamon that can run on a remote server
* to preallocate a set of worker nodes to reduce the start cost of each php program invocation
* to use the child process to also interpret the php program to reduce amount of system resources used

For more information see: https://www.php.net/manual/en/install.fpm.php

## Proposed Changes:

The php-fpm utilizes the Fast Common Gateway Interface (FCGI) protocol, and we propose to modify the code base to use the Simple Common Gateway Interface (SCGI).  The change should further improve performance.   

The following diagram depicts the general process flow for executing a CGI process via a FCGI and a SCGI server.  There are fewer steps when using the SCGI server.  Moverover, there is no need to perform any post processing of the CGI program.  This provides further opportunities to reduce the number of process concurrent processes that are needed.

![FCGI and SCGI Flow Diagram](https://github.com/csuntechlab/scgi-php-fpm/blob/main/images/FCGI-and-SCGI-Flow.png)

\* The PHP-FPM framework manages a pool of child processes to handle each FCGI request (which is not depicted in the above diagram).  

Moveover, PHP-FPM does not fork off a child process to execute the CGI program.  Instead it interpretes the PHP program within the process that is managing the FCGI request. This child process for executing a CGI process is needed, however, in the general case with the FCGI model.  

### Approaches
  * Step 1: Validate worthiness as a single process
    * Use the (SCGI wrapper)[https://github.com/csuntechlab/scgi-daemon]
    * Conduct performance comparison:
       * php execution via as a straight CGI process
       * php execution via as an FCGI process
       * php execution via the SCGI wrapper
  * Step 2: Validate worthiness a a pool of processes
    1. Update the (socket)[https://github.com/csuntechlab/socket] program
    1. Conduct performance comparison with N simultaneous runs of a program.
  * Step 3. Reengineer the current php implemnation
    (This would be a strickly reengineer task)


## Related Links
* Common Gateway Interface (CGI): https://en.wikipedia.org/wiki/Common_Gateway_Interface
* Simple Common Gateway Interface (SCGI):
  * https://en.wikipedia.org/wiki/Simple_Common_Gateway_Interface
  * http://python.ca/scgi/protocol.txt
* https://fastcgi-archives.github.io/FastCGI_A_High-Performance_Web_Server_Interface_FastCGI.html

## Our GitHub projects that supports this activity:
* [SCGI Daemon Project](https://github.com/csuntechlab/scgi-daemon): A mini-server to execute a CGI program over the SCGI protocol.
* [FSCGI Daemon Project](https://github.com/csuntechlab/fcgi-daemon): A mini-server to execute a CGI program over the FCGI protocol.
* [Socket Pool Project](https://github.com/csuntechlab/socket): The socket program with a pool of preforked workers
