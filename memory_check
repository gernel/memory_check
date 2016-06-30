#!/bin/bash

#for checking current memory usage
TOTAL_MEMORY=$( free -m | awk 'NR==2{printf "Memory Usage: %s/%sMB (%.2f%%)\n", $3,$2,$3*100/$2 }' )
echo $TOTAL_MEMORY

memuse=$(free -m | awk 'NR==2{printf "%.2f%", $3*100/$2}')

#for forcing memuse to be integer.
memuse1=${memuse/.*}

#for inform user to input 3 option and arguments.
if [ $# -ne 6 ]; then
	echo -e "Input 3 options with 3 arguments!\nexample: -c 90 -w 80 -e email@mine.com " >&2
	exit 0
fi

# getopts
while getopts ":c:w:e:" opt; do
	case $opt in
	 c)
	   critical=$OPTARG
	 ;;
	
	 w)
	   warning=$OPTARG
         ;;

	 e)
	   email=$OPTARG
	 ;;

         \?)
           echo "Invalid option, please use -c for critical -w for warning -e for email" >&2
           exit 1
         ;;

         :)
	   echo "Option - $OPTARG requires an argument." >&2
           exit 1
         ;;

	
	esac
	
	done

#email content directory
emaildir=/tmp/message.txt

#for critical ram usage
if [ $memuse1 -ge $critical ]; then
        topTenMem_usage=$(ps aux --sort -rss | head -n 10 | awk '{print $2,$4,$11}')
        message=$(echo -e "CRITICAL RAM usage in server Gernel. The threshold is $critical%. Current use is $memuse MiB. Please see below the TOP 10 processes\n$topTenMem_usage" > $emaildir)
        date=$(date +'%y%m%d %H:%M')

        #for email 
        mutt -s "$date memory check - critical" $email < /tmp/message.txt
        echo -e "Email Sent" >&2
        exit 2
fi

#for warning ram usage
if [ $warning -ge $critical ]; then
        echo -e "-c $critical% (Critical) should be higher than -w $warning% (Warning)." >&2
        exit 1

elif [ $memuse1 -ge $warning ]; then
        message="WARNING RAM usage in server Gernel. Current use is $memuse MiB."
        echo -e "$message" >&2
 
fi

exit 0
