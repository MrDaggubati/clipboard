# random_linux_cli
echo OS level Monitoring

--monitoring using top

top 15 processes sorted by memory
top -b -o +%MEM | head -n 22


finding free memory
 free -m | awk 'NR==3 {print $4 " MB"}'


# =============================================================================
--sar Monitroing checklists
# =============================================================================
      echo Monitoring Network Statstics from a File
       sar -n ALL -f /var/log/sa/sa23

      echo End of network statstics

       echo Monitoring Memory statistics between 10AM and 2 PM
      sar -C -s 13:10:00 -e 13:30:00 -f /var/log/sa/sa31

      sar -b -f /var/log/sa/sa10
      sar -b -s 10:40:00 -e 11:95:00 -f /var/log/sa/sa09

      echo end of memory stats monitoring

      echo CPU usage statistics for each CPU core and averaged across all CPU cores
      sar -u -P ALL

      swap space used
      sar -S 1 3

      --I/O monitoring
      sar -b 1 3

      ---block device level monitoring
       sar -d 1 1


       --run queue length
       sar -q 1 3

 # =============================================================================
 # IOPS Reads/sec and writes/sec separated with a slash:
 # =============================================================================
        iostat -dx <your disk name> | grep <your disk name> | awk '{ print $4"/"$5; }'

        # ry running iostat -dx and looking at the summary to get a feel for the output. You can also use iostat -dx 1 to show a
        # continuously refreshing output, which is useful for troubleshooting or live monitoring,

        # Using awk, field 4 will give you reads/second, while field 5 will give you writes/second.
        # Reads/second only:
        iostat -dx <your disk name> | grep <your disk name> | awk '{ print $4; }'

        # Writes/sec only:
        iostat -dx <your disk name> | grep <your disk name> | awk '{ print $5; }'

        # Reads/sec and writes/sec separated with a slash:
        iostat -dx <your disk name> | grep <your disk name> | awk '{ print $4"/"$5; }'

        # Overall IOPS (what most people talk about):
        iostat -d <your disk name> | grep <your disk name> | awk '{ print $2; }'

        # Note that you do not need to be root to run this either, making it useful for non-privileged users.
        # If you're just interested in sda, the following command will give you overall IOPS for sda:

        iostat -d sda | grep sda | awk '{ print $2; }'
        # If you want to add up the IOPS across all devices, you can use awk again:

        iostat -d | tail -n +4 | head -n -1 | awk '{s+=$2} END {print s}'

        # This produces output like so:
        dan@daneel ~ $ iostat -d | tail -n +4 | head -n -1 | awk '{s+=$2} END {print s}'
        18.88


# echo this tracks cpu and memory consumption by PID and includes even the child processes
ps -o pid,ppid,pgid,comm,%cpu,%mem  -u infa | {grep PID_PRINCIPAL}
# echo CPU usage stats monitor completed

#top 10 sorted by cpu
for i in $(ps -eo pid,pmem,pcpu| sort -k 3 -r|grep -v PID|head -10|awk '{print $1}');do diff -yw <(pidstat -p $i|grep -v Linux) <(ps -o euser,pri,psr,pmem,stat -p $i|tail);done

#top 10 sorted by cpu
for i in $(ps -eo pid,pmem,pcpu| sort -k 3 -r|grep -v PID|head -10|awk '{print $1}');do diff -yw <(pidstat -p $i|grep -v Linux) <(ps -o euser,pri,psr,pmem,stat -p $i|tail);done



top -bc -d 60 -n 1 | grep infa | sort -k 6 | head -10

top -bH -d 5 -n 49 -p

###
echo monitoring Top cpu , memory and block level I/O consumers
dstat --top-cpu --top-mem --top-bio

echo  to monitor process details and their threads
ps -eo nlwp,pid,args --sort nlwp

--to monitor continuously use below command
watch -n 1 'ps -eo nlwp,pid,args --sort -nlwp | head'

--zombie processes
ps aux | awk '"[Zz]" ~ $8 '

 
 ps -aef --forest | grep CUDAP_00_STAGE_00*
 
  ps -aef --forest | grep 50145


echo Sort Linux ‘ps’ output by memory (RAM), from high to low
echo Given that little piece of background information, heres how we can sort the ps command output by memory usage:
ps aux --sort -rss


echo process tree 
pstree -Gan -p 5600

pstree -np | grep pmrepagent

--threads of a process in realtime view
top -H -p <pid>

-- bit more details --<<>>--
ps --forest -o pid,%mem,%cpu,tty,stat,time,cmd -g $(ps -o sid= -p 9057)


pgrep -P $(pgrep supervisord) | xargs ps -o %mem,%cpu,cmd -p | awk '{memory+=$1;cpu+=$2} END {print memory,cpu}'



echo monitoring

-- which process are writing alot
 pidstat -d | sort -nr -k 4 | head -10


 pidstat -urd -h

 pidstat -p 13203

 echo for a process name
 pidstat -C "pmdtm"

 echo monitor IO stats for a process
 pidstat -p 30060 -d 1

 echo Paging Activity for a Specific Process
 pidstat -p 23493 -r 1

 echo set line 6000 if required
 echo display command name and arguments
 pidstat -C pmdtm -l

 echo memory, cpu consumption by a single processid
 ps -p <pid> -o %cpu,%mem,cmd

ps axo pid,etime,%cpu,%mem,cmd | grep 'pmdtm' | grep -v grep | awk '{print $1" "$2" "$3 $4 $16  $8 %15}' | sort -nr

#finding processes that are running more than one hour
find /proc -maxdepth 1 -user infa -type d -mmin +60 -exec basename {} \; | xargs ps  | awk '{ print $1 , $2 , $3 , $4 ,$5, $6 }'


# find process running as user builder, executed from interactive login pts, exclude bash processes
# the second column will be the elapsed time (etime)
# use awk on the 2nd column ($2) and get with a regex (~) the string before the -(ps etime prints in format dd-hh:mm:ss)
# If that value is larger than 7 (ie, been running more than seven days) print what is in the first column $1 , ie pid

#days!
ps -eo pid,etime,comm,user,tty | grep infa| grep -v bash |awk '$2~/-/ {if ($2>1) print  $1 , $2 , $3 , $4 ,$5, $6 }'

#minutes!
ps -eo pid,etime,comm,user,tty | grep infa| grep -v bash |awk '$2~/-/ {if ($3>1) print  $1 , $2 , $3 , $4 ,$5, $6 }'

--memory consuming processes
ps -e -orss=,args= | sort -b -k1,1n | pr -TW$COLUMNS | tail

 ps aux  | awk '{print $6/1024 " MB\t\t" $11}'  | sort -n
 echo script shows memory usage in MB 

 ps -eo rss,pid,user,command | sort -rn | head -10 | awk '{ hr[1024**2]="GB"; hr[1024]="MB";' 
 for (x=1024**3; x>=1024; x/=1024) {  if ($1>=x) { printf ("%-6.2f %s ", $1/x, hr[x]); break }  } } { printf ("%-6s %-10s ", $2, $3) }  { for ( x=4 ; x<=NF ; x++ ) { printf ("%s ",$x) } print ("\n") } 
 
/*
or use top

Or you could use... top.
press f to select the fields to display.
locate nTH (the number of threads) and press d to display it and s to make it the sort order
you can adjust its display position with → and then ↑ and ↓ and ⏎.
q to get back to the process list
press H if you want to see all the threads.
d to adjust the delay.
? for help.

*/

--File system monitoring
-- use du command to scan src files to see if there is any huge file that is being processed!!!
-- mere presence of a big file at SrcDirs

 --Lists files ordered by size
 du -ah folder_name/ | grep -v "/$" | sort -rh | head -6

 [ieowner@node1_ie folder_name]$ du -ah . | grep -v "/$" | sort -rh | head -6
5.5G    .

[ieowner@node1_ie folder_name]$


 --network stats

 netstat -s  | awk '{print  $1 ">" $2 ">" $3" "$4" " $5" " $6" " $7" " $8 " "$9 " "$10 " "$11}'
 



 -kiling hangging process for an integration service


 ##Files and folders and grep from linux
  # find files with a specific content.
  find . -type f -exec grep "UsrMgt_configuration.cfg" '{}' \; -print | sort

  find $PWD/ -type f -name '*20180818*.bin'  -exec readlink -f {} \;

  # find files that are created in a given data range.!
   #!/bin/bash
  find . -type f -newermt 2018-07-26 ! -newermt 2018-07-27

	 -- find folders
	 find . -maxdepth 1 -type d -printf '%f\n'

	 --Exclude current direcotry
	 find . -mindepth 1 -maxdepth 1 -type d -printf '%f\n'

	 -- list only files not the folders
	 find /dev -maxdepth 1 -name 'abc-*'

	 echo modified in last 3 days
	 find /home/exampleuser/ -name "*conf" -mtime 3 | grep s_mp_Def_Fundapps_FileName_Email

	 $ find srch_dir -cmin -60 # change time
	 or
	 $ find srch_dir -mmin -60 # modification time
	 or
	 $ find srch_dir -amin -60 # access time


	  find . -name "*csv" -cmin -120  -exec ls -lh {} \; | sort -k5


#finding a specific files in a folder
	find . -name "wf_CREDO_Write_REPORT_FundApp_UL_XML*"

#mailing

   #check mail queue ::
   find /var/spool/postfix/deferred -type f | wc -l

   # shows mailq count!
   NUM=`mailq | grep -E "Requests" | awk '{print $5}'`; if [ -z "$NUM" ]; then echo "0"; else echo $NUM; fi

   # check sendmail process
   ps auxw | grep sendmail | awk '{print"-p " $2}' | xargs strace -s 256 -f 2>&1 | grep -E $'@|(([0-9]+\.){3}[0-9]+)' | tee -a "/var/log/sendmail-logs.log"

	 echo "wflogs" | mailx -s "NNACA_00_MID03_00" -a "s_mp_MID03_NZNZB_SAPBP_REL3.log.20180817111807" emailaddress


	 echo "lst_faxml_email.txt" | mailx -s "lst_faxml_email.txt" -a  "/opt/Informatica/infa_shared/TgtFiles/ARTCH_11_MSSQL_00/lst_faxml_email.txt" emailaddress


	 echo "wflogs" | mailx -s "wf_CREDO" -a "wf_CREDO_Write_REPORT_FundApp_UL_XML.log.20180611172422.log" -a "wf_CREDO_Write_REPORT_FundApp_UL_XML.log.20180611123004.log" emailaddress

	 echo "tnsnames.ora_acc_9.6" mailx -s "tnsnames.ora_acc_9.6" -a "cds_tnsnames.ora" emailaddress

	 echo "odbc.ini" | mailx -s "odbc.ini" -a "odbc.ini" emailaddress


	 echo "Nodelogs" | mailx -s "INFA9.6 INFA Logs" -a "node.log" -a " node_jsf.log" -a "catalina.out" -a "exceptions.log" emailaddress

	 echo "IPC102 logs" | mailx -s "sarAug31"  -a "dgs_sar31.txt" emailaddress
-rw-rw----  1 infa infa   160000 Jul 23 16:15 dgsdetail.log

#finding a specific string from files
	  grep -rnw '/path/to/somewhere/' -e 'pattern'
		-r or -R is recursive,
		-n is line number, and
		-w stands for match the whole word.
		-l (lower-case L) can be added to just give the file name of matching files.
		Along with these, --exclude, --include, --exclude-dir flags could be used for efficient searching:

		This will only search through those files which have .c or .h extensions:

		grep --include=\*.{c,h} -rnw '/path/to/somewhere/' -e "pattern"
		This will exclude searching all the files ending with .o extension:

		grep --exclude=*.o -rnw '/path/to/somewhere/' -e "pattern"
		
		
For directories it's possible to exclude a particular directory(ies) through --exclude-dir parameter. For example, this will exclude the dirs dir1/, dir2/ and all of them matching *.dst/:

grep --exclude-dir={dir1,dir2,*.dst} -rnw '/path/to/somewhere/' -e "pattern"

/*
https://www.thegeekstuff.com/2011/07/iostat-vmstat-mpstat-examples/?utm_source=feedburner
https://unix.stackexchange.com/questions/225095/how-to-get-total-read-and-total-write-iops-in-linux
*/


--linux network latency check
		time nc -zw30 SRTZYORA0010.INSIM.BIZ 16580

		[ieowner@infanode1 ~]$ time nc -zw30 srpzyora0018.insim.biz 15505
		
		real    0m0.026s
		user    0m0.003s
		sys     0m0.003s

		##---- docs
		https://askubuntu.com/questions/7976/how-do-you-test-the-network-speed-between-two-boxes

		measure network speed without disk usage

		put “|dd if=/dev/zero bs=32k count=10000” /dev/null

		https://www.google.nl/search?q=network+latency+check+for+remote+servers+%2B+linux&ie=&oe=



pidstat -urd -h
pidstat -urd -h



---- Must gather stats
			pmap PID >  pmap_PID_n  
		#	atleast three times 
			netstat  > netstat_PID_n
		#	atleast three times 
			pstack PID > pstack_PID_n
		#	atleast three times 
			vmstat 2 3 > vmstat_PID 
			jstack PID > jstack_PID_n  (OR)   kill –QUIT <javaprocesspid>_c
			iostat 2 3 > iostat_PID 
			jmap -heap:format=b PID
			gcore PID
			top –d 5 –n 5 > top_out
			lsof –p  PID taken multiple times

		
		truss -f -r all -w all -p 23051 1>dbg.out 2>&1
		
