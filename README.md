##Jenkins pipeline-
#Error:
+ docker build -t sumon737/nodeapp:3 .
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/build?buildargs=%7B%7D&cachefrom=%5B%5D&cgroupparent=&cpuperiod=0&cpuquota=0&cpusetcpus=&cpusetmems=&cpushares=0&dockerfile=dockerfile&labels=%7B%7D&memory=0&memswap=0&networkmode=default&rm=1&shmsize=0&t=sumon737%2Fnodeapp%3A3&target=&ulimits=null&version=1": dial unix /var/run/docker.sock: connect: permission denied

$Solution:
chmod 777 /var/run/docker.sock

