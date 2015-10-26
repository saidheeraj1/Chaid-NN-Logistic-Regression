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

We can get the below graphs which compares three models in terms of ROI and profit.
 

RoiLR represents ROI for Logistic Regression model
RoiCH represents ROI for CHAID model
RoiNN represents ROI for Neural Networks model

 

PrLR represents Profit for Logistic Regression model
PrCH represents Profit for CHAID model
PrNN represents Profit for Neural Networks

 
Comparing the models for profit and ROI:
Model	Max profit	Max ROI in %
	Corresponding ROI in %	Corresponding profit
Logistic Regression	23925	124
	95	20205
CHAID	26015	112
	106	22485
NN	24465	125
	102	22815

We have to choose the best model among three. For this we can consider with respective to “Max Profit” or “Max ROI”. Calculations below help us to choose one among them.
(i)	If Max. Profit is considered, loss with respective to ROI =
106-125/125 = -0.152 = -15.2%
(ii)	If Max. ROI is considered, loss with respective to profit =
22815-26015/26015 = -0.123 = -12.3%
Considering both, case (ii) is better as it is low compared to case (i). As the loss percentage is less for “Max ROI” method it is better to choose that over “max profit”.
Hence cut off is made with respective to ROI. 
The model which optimizes ROI (125%) is Neural Network (NN) model.
