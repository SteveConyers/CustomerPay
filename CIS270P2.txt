**Free

// Program:  CIS270P2
// Programmer:  Steven Conyers
// Date Created: 1/22/2023
// Revisions:
// Purpose:  This program is used to update payment amounts and and amount owed
//           by using transaction file GTCPAY and master record file MYCSTP.

// -----Control Statement----------------------------------
Ctl-opt option(*srcStmt: *nodebugio);

// -----Declare Files--------------------------------------
DCL-F GTCPAYP Disk;
DCL-F MYCSTP  Disk Usage(*Update) Keyed;
DCL-F CIS270P2 Printer Oflind(Endofpage);


// ------Declare Standalone Variables----------------------
DCL-S EndofPage Ind Inz(*On); //Overflow Indicator

DCL-S NOCUS Char(34);
// ------Main Procedures-----------------------------------
Read GTCPAYP;

Dow Not %Eof;

  Chain CPHONE MYCSTP;

  IF %Found(MYCSTP);
    NOCUS = *Blanks;
    Eval(H) AMTOWE = AMTOWE - AMTPD;
    PAYDAT = DATRCV;
    PAYDATL = DATRCVL;
    Update CUSREC %Fields(AMTOWE:PAYDAT:PAYDATL);
  Else;
    NOCUS = '*** Customer Record Not Found ***';
    INVALCUS += 1;
  ENDIF;

  If EndofPage;
    Write Heading;
    EndofPage = *off;
  Endif;

  Write Detail;

  Read GTCPAYP;

Enddo;

Write Error;

*Inlr = *On;
Return;
 