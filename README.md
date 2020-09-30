# Bus-reservation-using-SQL
>>>>>>>>>>>>>>>>>>>>CREATE TABLE<<<<<<<<<<<<<<<<<<<<

create table bus_details(bus_name char(15) primary key,total_seats number(3),reserved_seats number(3));

create table busreservation_status(bus_name char(15) references bus_details(bus_name),seat_id number(3),reserved char(2) check (reserved in('y','n')),customer_name char(15));



>>>>>>>>>>>>>>>>>>>>ENTER BUS DETAILS<<<<<<<<<<<<<<<<<<<<

declare
 bname char(15);
 tot number(3);
 resv number(3);
 cursor cur is select * from bus_details;
 begin
 insert into bus_details values('&bus_name',&total_seats,0);
open cur;
 loop
fetch cur into bname,tot,resv;
 if cur%found then
for i in 1..tot
loop
 insert into busreservation_status values(bname,i,'n',null);
end loop;
else
exit;
end if;
end loop;
 close cur;
 end;
/


PL/SQL procedure successfully completed.
SQL> select * from bus_details;

BUS_NAME	TOTAL_SEATS RESERVED_SEATS
--------------- ----------- --------------
aa			  3		 0

SQL> select * from busreservation_status;

BUS_NAME	   SEAT_ID RE CUSTOMER_NAME
--------------- ---------- -- ---------------
aa			 1 n
aa			 2 n
aa			 3 n



 
******************************* BUS RESERVATION **********************
declare
cname char(15);
bname char(15);
sid number(3);
tot number(3);
resv number(3);
begin
cname:='&cname';
bname:='&bname';
select total_seats into tot from bus_details where bus_name=bname;
select reserved_seats into resv from bus_details where bus_name=bname;
if tot>resv then
            select MIN(seat_id) into sid from busreservation_status where bus_name=bname and reserved='n';

update busreservation_status set reserved='y' where bus_name=bname and seat_id=sid;

update busreservation_status set customer_name=cname where bus_name=bname and seat_id=sid;

update bus_details set reserved_seats=reserved_seats+1 where bus_name=bname;
end if;
dbms_output.put_line('No seat avalable');
           
end;
/
************************  OUTPUT ***********************************

select * from bus_details;
select * from busreservation_status;

SQL> select * from bus_details;

BUS_NAME	TOTAL_SEATS RESERVED_SEATS
--------------- ----------- --------------
aa			  3		 1

SQL> select * from busreservation_status;

BUS_NAME	   SEAT_ID RE CUSTOMER_NAME
--------------- ---------- -- ---------------
aa			 1 y  suman
aa			 2 n
aa			 3 n



****************** BUS CANCELATION ************************
declare
cname char(15);
bname char(15);
sid number(3);
resv number(3);
begin
cname:='&cname';
bname:='&bname';
select seat_id into sid from busreservation_status where bus_name=bname and customer_name=cname;

select reserved_seats into resv from bus_details where bus_name=bname;

if resv>0 then
            update busreservation_status set reserved='n' where bus_name=bname and seat_id=sid;
            update busreservation_status set customer_name=null where bus_name=bname and seat_id=sid;
            update bus_details set reserved_seats=reserved_seats-1 where bus_name=bname;
end if;

 dbms_output.put_line('Cancelation not allow');
end;
/

************************  OUTPUT ***********************************

SQL> select * from bus_details;

BUS_NAME	TOTAL_SEATS RESERVED_SEATS
--------------- ----------- --------------
aa			  3		 0

SQL> select * from busreservation_status;

BUS_NAME	   SEAT_ID RE CUSTOMER_NAME
--------------- ---------- -- ---------------
aa			 1 n
aa			 2 n
aa			 3 n
