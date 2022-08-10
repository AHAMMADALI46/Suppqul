# Supp_dm
/*race in suppqual*/
data race (keep= usubjid race);
set raw.demowide;
usubjid=catx('-',study,stdysite, patient);
where index(race,"other")>0;
run;

/*getting race info from sdtm_dm*/
data rac (keep= studyid usubjid domain race);
set sdt.sdtm_dm;
where race in ("other" "Multiple");
run;

/*Merge Race and Rac*/
Proc sort data=race;
by usubjid;
run;
proc sort data=rac;
by usubjid;
run;

data supp_race;
merge rac(in=A) race (rename=(race=oth));
by usubjid;
if A;
run;

data supp_rac (drop=domain race oth);
set supp_race;
rdomain=domain;
idvar=" ";
idvarval=" ";
qnam="raceoth";
length qlabel $ 40;
qlabel="race other";
qval=substr(oth,8);
Qeval="Investigator";
qorig="CRF";
run;
**********************************************1
/*Random Number*/
data rno (keep=usubjid randn;
set raw.random;
usubjid=catx('-',study,stdysite, patient);
rdomain="DM";
where randn ne ' ';
run;
/*Merge with SDTM_DM*/
Proc sort data=sdt.sdtm_dm out=dm (keep=studyid domain usubjid);
by usubjid;
run;
proc sort data=rno;
by usubjid;
run;

data randm;
merge dm(in=A) rno;
by usubjid;
if A;
run;

data supp_rnd (drop=domain randn);
set randm;
rdomain=domain;
idvar=" ";
idvarval=" ";
qnam="randno";
length qlabel $ 40;
qlabel="randomization number";
qval=randn;
Qeval="Investigator";
qorig="CRF";
run;
**************************2;
/*getting subject initials*/
data subjini (keep=studyid usubjid race);
set raw.demowide;
studyid=study;
usubjid=catx('-',study,stdysite, patient);
where initials ne ' ';
run;

Proc sort data=dm;
by usubjid;
run;
proc sort data=subjini;
by usubjid;
run;

data subj;
merge dm(in=A) subjini;
by usubjid;
if A;
run;

data supp_initials (drop=domain initals);
set subj;
rdomain=domain;
idvar=" ";
idvarval=" ";
qnam="randno";
length qlabel $ 40;
qlabel="subject initials";
qval=initals;
Qeval="Investigator";
qorig="CRF";
run;
*************************************3;
/*add 1, 2 and 3*/
data sdt.supp_dm;
set supp_rac supp_rand supp_initials;
run;

