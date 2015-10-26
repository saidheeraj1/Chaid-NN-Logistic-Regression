# Chaid-NN-Logistic-Regression
SAS project

data holdout;

set  Lec7.ChaidOutput;

if   respholdout>.;

run;
 
proc sort data=holdout;

by descending VR_RESP;

run;
 

data RespAnalCHAID (keep=reccount respholdout cumcount cumresp);

set  holdout;

reccount=1;

cumcount+reccount;

cumresp+respholdout;

run;

data RespAnalCHAID;

set  RespAnalCHAID;

RespPct=cumresp/106;

CountPct=cumcount/1806;

run;

data cutpoint;

set  RespAnalCHAID;

lagCountPct=lag(CountPct);

if   CountPct ge .75 and lagCountPct lt .75 then output;

run;

proc print data=cutpoint;

run;



 


data holdout;

set  Lec7.NNOutput;

if   respholdout>.;

run;

proc sort data=holdout;

by descending VN_RESP;

run;

data RespAnalNN (keep=reccount respholdout cumcount cumresp);

set  holdout;

reccount=1;

cumcount+reccount;

cumresp+respholdout;

run;

data RespAnalNN;

set  RespAnalNN;

RespPct=cumresp/106;

CountPct=cumcount/1806;

run;


data cutpoint;

set  RespAnalNN;

lagCountPct=lag(CountPct);

if   CountPct ge .75 and lagCountPct lt .75 then output;

run;

proc print data=cutpoint;

run;
 
data  compare;

merge Lec7.RespAnalLR    (keep=cumresp rename=(cumresp=LRCumResp))

           RespAnalNN    (keep=cumresp rename=(cumresp=NNCumResp))
           
	   RespAnalChaid (keep=cumresp rename=(cumresp=CHCumResp))
	  ;
mailed=15*_n_;

Vcost=mailed;

Fcost=8000 ;

Tcost=Vcost+Fcost;

RevLR=LRCumResp*500;

RevCH=CHCumResp*500;

RevNN=NNCumResp*500;

PrLR=RevLR-Tcost;

PrCh=RevCH-Tcost;

PrNN=RevNN-Tcost;

RoiLR=PrLR/Tcost;

RoiCH=PrCH/Tcost;

RoiNN=PrNN/Tcost;

run;



