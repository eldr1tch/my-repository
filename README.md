# duocacad
-- averiguar IP con ifconfig
-- agregar o modificar la ip y nombre de la maquina en el archivo /etc/hosts

192.168.XXX.XXX dbtest.duoc.cl dbtest

-- Incorporar en el archivo /etc/sysctl.conf los parámetros del kernel 
-- recomendados para la intalación de Oracle 

# Parámetros del Kernel para la instalación de Oracle 11g

kernel.shmmni = 4096 
kernel.shmmax = 4398046511104  
kernel.shmall = 1073741824  
kernel.sem = 250 32000 100 128
fs.aio-max-nr = 1048576
fs.file-max = 6815744
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048586


-- incorporar las modificaciones realizadad al kernel

   /sbin/sysctl -p


-- modificar los límites del shell en el archivo 
-- /etc/security/limits.conf  

# Limites del shell para usuarios oracle y grid
oracle         soft    nproc           2047
oracle         hard    nproc          16384
oracle         soft    nofile          4096
oracle         hard    nofile         65536
oracle         soft    stack          10240



-- crear grupos requeridos según el tipo de instalación
-- y creación del usuario oracle dueño de la instalación

[root@dbtest ~]# groupadd oinstall
[root@dbtest ~]# groupadd dba
[root@dbtest ~]# groupadd oper
[root@dbtest ~]# useradd -g oinstall -G dba,oper oracle

[root@dbtest ~]# clear
[root@dbtest ~]# cd /home/oracle
[root@dbtest oracle]# ls
[root@dbtest oracle]# ls -all

-- modificar la password del usuario oracle

-- agregar las variables de ambiente en el archivo profile del usuario oracle 
-- /home/oracle/.bash_profile

# Oracle Settings
export TMP=/tmp
TMPDIR=$TMP; export TMPDIR

ORACLE_HOSTNAME=dbtest.duoc.cl; export ORACLE_HOSTNAME
ORACLE_BASE=/u01/app/oracle; export ORACLE_BASE
ORACLE_HOME=$ORACLE_BASE/product/11.2.0/db_home1; export ORACLE_HOME
ORACLE_SID=orcl;export ORACLE_SID
ORACLE_UNQNAME=orcl;export ORACLE_UNQNAME
NLS_LANG=AMERICAN_AMERICA.AL32UTF8; export NLS_LANG

PATH=/usr/sbin:$PATH:$ORACLE_HOME/bin; export PATH
LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib; export LD_LIBRARY_PATH
CLASSPATH=$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib; export CLASSPATH

umask 022


-- creacion de la OFA o estructura de directorios
-- asignación de propietario y concesión de permisos 

[root@dbtest ~]# mkdir -p /u01/app/oracle/product/11.2.0/db_home1
[root@dbtest ~]# mkdir -p /u02/oradata
[root@dbtest ~]# chown -R oracle:oinstall /u01 /u02
[root@dbtest ~]# chmod -R 775 /u01 /u02
[root@dbtest ~]# 

-- verificar los paquetes instalados
-- instalar todos los paquetes no instalados

rpm -q --qf '%{NAME}-%{VERSION}-%{RELEASE}(%{ARCH})\n' binutils \
compat-libstdc++-33 \
elfutils-libelf \
elfutils-libelf-devel \
gcc \
gcc-c++ \
glibc \
glibc-common \
glibc-devel \
glibc-headers \
ksh \
libaio \
libaio-devel \
libgcc \
libstdc++ \
libstdc++-devel \
make \
sysstat \
unixODBC \
unixODBC-devel


-- inhabilitar el secure linux modificando el archivo
-- /etc/selinux/config

setear como permissive la variable SELINUX

-- apagar el cortafuegos de linux
}
[root@dbtest ~]# sudo systemctl disable firewalldd
[root@dbtest ~]# sudo systemctl stop firewalld


-- arreglar error de incompatibilidad
-- como usuario oracle edita el archivo

nano $ORACLE_HOME/sysman/lib/ins_emagent.mk

-- Dentro del archivo, ubica la sección #  emdctl, busca la linea 

$(MK_EMAGENT_NMECTL)

y remplázala por 

$(MK_EMAGENT_NMECTL) -lnnz11

-- Salva el archivo y presiona Retry (Reintentar) en el cuadro de diálogo
