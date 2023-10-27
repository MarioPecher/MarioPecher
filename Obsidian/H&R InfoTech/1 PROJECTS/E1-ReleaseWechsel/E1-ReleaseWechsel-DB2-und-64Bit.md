[[E1]] [[IBM]] [[DB2]]

# E1-ReleaseWechsel-DB2-und-64Bit
## Termine
- 26.04.2022 erstes Meeting


- [[LPAR]]names
	- [[sdacdbn533]]
	- [[sdacdbn534]]

- [[HA]]-IP-lables
	- [[sdace1d533]]
	- [[sdace1d534]]
	- [[sdace1d539]]


- vFC vor vETHERNET da LUN Mapping erfolgen kann während man die ETHER-Channel config macht
- 
# [[ETHERNET-ADAPTER]]
IP-Range (15 Adressen)
### HUR E1 - network
#### VLANID - 511

10.225.101.60 - sdacind539 - E1 SSP InnoTest HA IP-Label
10.225.101.61 - sdacind533 - E1 SSP InnoTest Inst3 IP-Label
10.225.101.62 - sdacind534 - E1 SSP InnoTest Inst4 IP-Label

10.225.101.227 - 
10.225.101.228 - 
10.225.101.229 - 
10.225.101.230 - sdacbss923d - E1 BSSV Server - IP-Label
10.225.101.231 - erp923d - E1 EMEA ApplicationSever IP-Label
10.225.101.232 - erp924d - E1 AISA ApplicationSever IP-Label
10.225.101.233 - [[sdacdbn533]] - LPAR
10.225.101.234 - [[sdacdbn534]] - LPAR
10.225.101.235 - web923d - E1 EMEA WebSever IP-Label
10.225.101.236 - web924d - E1 EMEA ApplicationSever IP-Label
10.225.101.237 - sdace1d533 - E1 DB2 Inst1 IP Label
10.225.101.238  - sdace1d534- E1 DB2 Inst2 IP Label
10.225.101.239 - SDACE1D539 - E1 DB2 HA IP-Label

10.225.101.240 - LEER

### network - HUR PVM Cluster interconnect
#### VLANID - 589

192.168.100.233 - sdacdbn533p - GPFS
192.168.100.234 - sdacdbn534p - GPFS

192.168.100.235 - sdace1d533_hadr - E1 inst1
192.168.100.236 - sdace1d534_hadr - E1 inst2
192.168.100.237 - sdacind533_hadr - SSP inst3
192.168.100.238 - sdacind534_hadr - SSP inst4

### network - HUR PVM Cluster
#### VLANID - 567

172.20.101.133 - sdacdbn533_nim
172.20.101.134 - sdacdbn534_nim


	dbn921d ?!?
erp921d -> erp923d
erp922d -> erp924d
web921d -> web923d
web922d -> web924d
	bip921d -> bip923d
bss921d -> bss923d
ind533 -> 
ind534 -> 
ind539 -> 


Matthias Freitag 01.09.2023 09:03
• OW_PROD (dann OW_DEV):
- JDE
- PRODCTL
- PRODDTA

- OWSH920:
	- DD920
	- DV920
	- OL920
	- PD920
	- PY920
	- PS920
	- SVM920
	- SY920

Groups:
- JDEADMIN
- JDEUSER

Matthias Freitag 01.09.2023 09:03 
• AIX User
- jde920
- jdeas920

mkgroup oinstall
useradd -c "Oracle Install User" -m oracle

dnf-aixtoolbox nachfolger der yum-aixtoolbox

set |grep DB2

muss als instance User folgenden Output haben:

set |grep DB2
DB2DIR=/opt/IBM/db2/V11.5
DB2INSTANCE=db2inst2
DB2LIB=/db2home/db2inst2/sqllib/lib
DB2_HOME=/db2home/db2inst2/sqllib
DB2_NET_CLIENT_PATH=''

db2set


ATTENTION mount /opt/IBM !!!
lv /opt/IBM wird über einen Pfad gemounted der schon Daten enthält !!!


Thomas Schwaack 18.09.2023 11:31 • 
db2 update db cfg for ow_dev using LOGARCHMETH1 DISK:/e1db2backupfs/backups/ow_dev/arc_logs/

db2 update db cfg for ow_ps920 using LOGARCHMETH1 DISK:/e1db2backupfs/backups/ow_ps920/arc_logs/

db2 update db cfg for owsh920 using LOGARCHMETH1 DISK:/e1db2backupfs/backups/owsh920/arc_logs/

db2 "bind db2look.bnd BLOCKING ALL GRANT PUBLIC sqlerror continue"
db2 "bind db2lkfun.bnd BLOCKING ALL GRANT PUBLIC sqlerror continue"

db2 connect to ow_ps920
db2 "CALL SYSPROC.ADMIN_REVALIDATE_DB_OBJECTS(NULL,NULL,NULL)"
db2rbind ow_PS920 all -l db2rbind_ow_ps920.log
db2 terminate

db2 connect to owsh920
db2 "CALL SYSPROC.ADMIN_REVALIDATE_DB_OBJECTS(NULL,NULL,NULL)"
db2 terminate
db2rbind owsh920 all -l db2rbind_owsh920.log

db2 connect to ow_dev
db2 "CALL SYSPROC.ADMIN_REVALIDATE_DB_OBJECTS(NULL,NULL,NULL)"
db2 terminate
db2rbind ow_dev all -l db2rbind_ow_dev.log

Thomas Schwaack 18.09.2023 11:32 • 
client instance
/opt/IBM/db2/rtcl/instance/db2icrt -s client db2inst5
db2 catalog tcpip node SDACE1D remote SDACE1D539.hr.local SERVER 60000 remote_instance db2inst1
db2 catalog database OWSH920 as JDE920 at node SDACE1D authentication server
db2 catalog database OW_DEV as JDE_DEV at node SDACE1D authentication server
db2 catalog database OW_PS920 as JDE_P920 at node SDACE1D authentication server
db2 update cli cfg for section JDE920 USING LOBCACHESIZE 2500000
db2 update cli cfg for section COMMON USING DYNAMIC 1

Thomas Schwaack 18.09.2023 11:36 • user ps920dta und ps920ctl über smitty angelegt - > Kennwörter liegen im Keepass für CNC. Bitte dort alle DB-Kennwörter und Instanz-User Kennwörter ablegen.

Thomas Schwaack 18.09.2023 11:36 • user ps920dta und ps920ctl über smitty angelegt - > Kennwörter liegen im Keepass für CNC. Bitte dort alle DB-Kennwörter und Instanz-User Kennwörter ablegen.

- [ ] AIX Username length 24
- [ ] pxjde dbadmin rechte
- [ ] pw - dynamic - username+iud or random