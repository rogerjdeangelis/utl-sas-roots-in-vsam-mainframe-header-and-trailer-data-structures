# utl-sas-roots-in-vsam-mainframe-header-and-trailer-data-structures
Processing IBM data structures was a big part of SAS early years.  
    %let pgm=utl-sas-roots-in-vsam-mainframe-header-and-trailer-data-structures;

      Processing IBM data structures was a big part of SAS early years.

      SAS simplified processing of complex IBM record types like RMF and SMF.
      SMF and RMF record IBM mainframe performance.

      Processing header and trailer records wa part of IBM VSAM data structures

      Below is an simplified example of header and trailer processing made easy by SAS and WPS

      Solutions
        SAS:  Long record with header with mutiple trailer records
        WPS:  Long record with header with mutiple trailer records


    github
    https://tinyurl.com/yyejkv3r
    https://github.com/rogerjdeangelis/utl-sas-roots-in-vsam-mainframe-header-and-trailer-data-structures

    Realted StackOverflow
    https://tinyurl.com/bddavzje
    https://stackoverflow.com/questions/75451911/complete-columns-based-on-values-that-precede-data-as-table-format-in-sas


    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|

    filename vsm "%sysfunc(pathname(work))/vsm.txt";

    data _null_;
      file vsm;
      input;
      put _infile_;
    cards4;
    IBM_QTRS 2001 3 4 104 108 105 108 114 118 115 110 124 128 125 120
    GE_QTRS  2001 3 4 134 138 135 130 144 148 145 140 154 158 155 158
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* The header has the name of the stock, the starting  year and the number of quarters.                                   */
    /* The trailing blocks of length 4 have the quarterly stock prices.                                                       */
    /*                                                                                                                        */
    /* f:\wrk\_TD44236_T7610_\vsm.txt                                                                                         */
    /*                                                                                                                        */
    /* IBM_QTRS 2001 3 4 104 108 105 108 114 118 115 110 124 128 125 120                                                      */
    /* GE_QTRS  2001 3 4 134 138 135 130 144 148 145 140 154 158 155 158                                                      */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*  Up to 40 obs from last table WORK.WANT total obs=6 09APR2023:17:05:31                                                 */
    /*                                                                                                                        */
    /*  Obs    STOCK     YR     QTR1    QTR2    QTR3    QTR4                                                                  */
    /*                                                                                                                        */
    /*   1      IBM     2001     104     108     105     108                                                                  */
    /*   2      IBM     2002     114     118     115     110                                                                  */
    /*   3      IBM     2003     124     128     125     120                                                                  */
    /*                                                                                                                        */
    /*   4      GE      2001     134     138     135     130                                                                  */
    /*   5      GE      2002     144     148     145     140                                                                  */
    /*   6      GE      2003     154     158     155     158                                                                  */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
     ___  __ _ ___   _ __  _ __ ___   ___ ___  ___ ___
    / __|/ _` / __| | `_ \| `__/ _ \ / __/ _ \/ __/ __|
    \__ \ (_| \__ \ | |_) | | | (_) | (_|  __/\__ \__ \
    |___/\__,_|___/ | .__/|_|  \___/ \___\___||___/___/
                    |_|
    */
    proc datasets lib=work nodetails nolist mt=view mt=data ;
     delete want;
    run;quit;

    data want;
    infile "%sysfunc(pathname(work))/vsm.txt";
    retain stock year;
    array quarters qtr1-qtr4;
    input stock$ start trailers qtrs @ ;
    do year= start to (start+2);
       do qdx=1 to qtrs;
         input quarters[qdx] @;
       end;
         output ;
    end;
    drop trailers start qtrs qdx;
    run;quit;

    /*
    __      ___ __  ___   _ __  _ __ ___   ___ ___  ___ ___
    \ \ /\ / / `_ \/ __| | `_ \| `__/ _ \ / __/ _ \/ __/ __|
     \ V  V /| |_) \__ \ | |_) | | | (_) | (_|  __/\__ \__ \
      \_/\_/ | .__/|___/ | .__/|_|  \___/ \___\___||___/___/
             |_|         |_|
    */

    %utl_submit_wps64("

    data want;
    infile '%sysfunc(pathname(work))/vsm.txt';
    retain stock year;
    array quarters qtr1-qtr4;
    input stock$ start trailers qtrs @ ;
    do year= start to (start+2);
       do qdx=1 to qtrs;
         input quarters[qdx] @;
       end;
         output ;
    end;
    drop trailers start qtrs qdx;
    run;quit;
    proc print;
    run;quit;
    ");

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* The WPS System                                                                                                         */
    /*                                                                                                                        */
    /* Obs     STOCK      YEAR    QTR1    QTR2    QTR3    QTR4                                                                */
    /*                                                                                                                        */
    /*  1     IBM_QTRS    2001     104     108     105     108                                                                */
    /*  2     IBM_QTRS    2002     114     118     115     110                                                                */
    /*  3     IBM_QTRS    2003     124     128     125     120                                                                */
    /*  4     GE_QTRS     2001     134     138     135     130                                                                */
    /*  5     GE_QTRS     2002     144     148     145     140                                                                */
    /*  6     GE_QTRS     2003     154     158     155     158                                                                */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */









