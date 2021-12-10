1. a=1
   b=2
   c=a+b
   d=$a+$b
   e=$(($a+$b))

c=1+2;d=1+2;e=3
c и d - выводит простое значение переменной, e - выводит значение функции $(()) / $[]

2. while ((1==1)) // нет скобки
   do
       curl https://localhost:4757
       if (($? != 0))
       then
           date >> curl.log
       fi
       else exit // при успехе прерываем цикл
       sleep 5 // для экономии размера лога :)
   done


3. #!/usr/bin/env bash

#IP to check
IP1="192.168.0.1"
IP2="173.194.222.113"
IP3="87.250.250.242"
#Port
p="80"
#Date
date=$(date)

array_str=($IP1 $IP2 $IP3) // Лишние строки, зато переменные :)
echo check web on ${array_str[@]}
for i in {1..5}
do
sleep 5
        for h in ${array_str[@]}
        do
                curl http://$h:$p > /dev/null
                if (($? != 0))
                    then
                    echo "$date Web on $h is dead" >> web.log
                    else
                    echo "$date Web on $h is alive" >> web.log
                fi
        done
done

#Или для каждого отдельно...лог по одной строке.
#IP=${array_str[0]}
#echo check web on $IP
#for i in {1..5}
#do
#curl http://$IP:$p
#done
#if (($? != 0))
#then
#echo "$date Web on $IP is dead" >> web.log
#else
#echo "$date Web on $IP is alive" >> web.log
#fi

4. #!/usr/bin/env bash
IP1=192.168.90.1
IP2=173.194.222.113
IP3=87.250.250.242
p=80
date=$(date)
timeout=5

array_str=($IP1 $IP2 $IP3)
res=0

while (($res == 0))
do
    for h in ${array_str[@]}
    do
        curl -Is --connect-timeout $timeout $h:$p > /dev/null
        res=$?
        if (($res != 0))
        then
            echo "$date ERROR on " $h status=$res >> web2.log
        fi
    done
done
