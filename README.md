# utl-all-combination-of-of-rows-of-dataset-in-wps-r-best-in-wps-r-python
All combination of of rows of dataframe in wps r; 
    %let pgm=utl-all-combination-of-of-rows-of-dataset-in-wps-r-best-in-wps-r-python;

    All combination of of rows of dataframe in wps r;

     SOLUTIONS

         1 WPS R
         2 wps sql
         3 wps r sql
         4 wps python sql
         5 fithub related repos


    github
    http://tinyurl.com/459mkfnf
    https://github.com/rogerjdeangelis/utl-all-combination-of-of-rows-of-dataset-in-wps-r-best-in-wps-r-python

    PROBLEM

      All possible pairing of couples

    /**************************************************************************************************************************/
    /*                        |                                                  |                                            */
    /*  SD1.HAVE total obs=4  |                                                  |     SD1.WANT total obs=6                   */
    /*                        |                                                  |                                            */
    /*  Obs  ID  A    B       |    SIMPLE R SOLUTION                             |     ID   ID1  A    B       A1    B1        */
    /*                        |    =================                             |                                            */
    /*   1   A  MIKE MARY     |    cmb <- combn(seq_len(nrow(have)), 2);         |      A   D   MIKE MARY    LEON  JILL       */
    /*   2   B  JACK JUNE     |    want<-cbind(have[cmb[1,],], have[cmb[2,],])   |      A   B   MIKE MARY    JACK  JUNE       */
    /*   3   C  MARK ROSE     |                                                  |      A   C   MIKE MARY    MARK  ROSE       */
    /*   4   D  LEON JILL     |    WPS R and Python                              |      B   C   JACK JUNE    MARK  ROSE       */
    /*                        |    ================                              |      B   D   JACK JUNE    LEON  JILL       */
    /*                        |    select                                        |      C   D   MARK ROSE    LEON  JILL       */
    /*                        |       l.id as id                                 |                                            */
    /*                        |      ,r.id as id1                                |                                            */
    /*                        |      ,l.a                                        |                                            */
    /*                        |      ,r.a  as a1                                 |                                            */
    /*                        |      ,l.b                                        |                                            */
    /*                        |      ,r.b  as b1                                 |                                            */
    /*                        |    from                                          |                                            */
    /*                        |      sd1.have as l left join sd1.have as r       |                                            */
    /*                        |    on                                            |                                            */
    /*                        |      l.id < r.id                                 |                                            */
    /*                        |    where                                         |                                            */
    /*                        |      not missing(r.id)                           |                                            */
    /*                        |    order                                         |                                            */
    /*                        |      by l.id                                     |                                            */
    /*                        |                                                  |                                            */
    /**************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
    input ID$ A$ B$;
    cards4;
    A  MIKE MARY
    B  JACK JUNE
    C  MARK ROSE
    D  LEON JILL
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* SD1.HAVE total obs=4                                                                                                   */
    /*                                                                                                                        */
    /* Obs    ID     A       B                                                                                                */
    /*                                                                                                                        */
    /*  1     A     MIKE    MARY                                                                                              */
    /*  2     B     JACK    JUNE                                                                                              */
    /*  3     C     MARK    ROSE                                                                                              */
    /*  4     D     LEON    JILL                                                                                              */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
    / | __      ___ __  ___   _ __  _ __ ___   ___   _ __
    | | \ \ /\ / / `_ \/ __| | `_ \| `__/ _ \ / __| | `__|
    | |  \ V  V /| |_) \__ \ | |_) | | | (_) | (__  | |
    |_|   \_/\_/ | .__/|___/ | .__/|_|  \___/ \___| |_|
                 |_|         |_|
    */
    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;

    %utl_submit_wps64('
    libname sd1 sas7bdat "d:/sd1";
    proc r;
    export data=sd1.have r=have;
    submit;
    cmb <- combn(seq_len(nrow(have)), 2);
    want<-cbind(have[cmb[1,],], have[cmb[2,],]);
    want;
    endsubmit;
    import r=want data=sd1.want ;
    run;quit;
    ');

    /*___                                   _
    |___ \  __      ___ __  ___   ___  __ _| |
      __) | \ \ /\ / / `_ \/ __| / __|/ _` | |
     / __/   \ V  V /| |_) \__ \ \__ \ (_| | |
    |_____|   \_/\_/ | .__/|___/ |___/\__, |_|
                     |_|                 |_|
    */

    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;

    %utl_submit_wps64('
    options validvarname=any;
    libname sd1 "d:/sd1";
    Proc sql;
      create
         table sd1.want as
      select
         l.id as id
        ,r.id as id1
        ,l.a
        ,r.a  as a1
        ,l.b
        ,r.b  as b1
      from
        sd1.have as l left join sd1.have as r
      on
        l.id < r.id
      where
        not missing(r.id)
      order
        by l.id
    ;quit;
    proc print;
    run;quit;
    endsubmit;
    ');

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* The WPS R System                                                                                                       */
    /*                                                                                                                        */
    /* obs    ID    ID1     A       A1      B       B1                                                                        */
    /*                                                                                                                        */
    /*  1     A      B     MIKE    JACK    MARY    JUNE                                                                       */
    /*  2     A      C     MIKE    MARK    MARY    ROSE                                                                       */
    /*  3     A      D     MIKE    LEON    MARY    JILL                                                                       */
    /*  4     B      C     JACK    MARK    JUNE    ROSE                                                                       */
    /*  5     B      D     JACK    LEON    JUNE    JILL                                                                       */
    /*  6     C      D     MARK    LEON    ROSE    JILL                                                                       */
    /*                                                                                                                        */
    /**************************************************************************************************************************/
    /*____                                         _
    |___ /  __      ___ __  ___   _ __   ___  __ _| |
      |_ \  \ \ /\ / / `_ \/ __| | `__| / __|/ _` | |
     ___) |  \ V  V /| |_) \__ \ | |    \__ \ (_| | |
    |____/    \_/\_/ | .__/|___/ |_|    |___/\__, |_|
                     |_|                        |_|
    */

    proc datasets lib=sd1 nolist nodetails;delete want; run;quit;

    options validvarname=any;
    libname sd1 "d:/sd1";

    %utl_submit_wps64x('
    libname sd1 "d:/sd1";
    proc r;
    export data=sd1.have r=have;
    submit;
    library(sqldf);
    want <- sqldf("
      select
         l.id as id
        ,r.id as id1
        ,l.a
        ,r.a  as a1
        ,l.b
        ,r.b  as b1
      from
        have as l left join have as r
      on
        l.id < r.id
      where
        r.id is not null
      order
        by l.id
    ");
    want;
    endsubmit;
    import data=sd1.want r=want;
    run;quit;
    ');

    proc print data=sd1.want width=min;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* The WPS R System                                                                                                       */
    /*                                                                                                                        */
    /*   ID ID1    A   A1    B   B1                                                                                           */
    /*                                                                                                                        */
    /* 1  A   B MIKE JACK MARY JUNE                                                                                           */
    /* 2  A   C MIKE MARK MARY ROSE                                                                                           */
    /* 3  A   D MIKE LEON MARY JILL                                                                                           */
    /* 4  B   C JACK MARK JUNE ROSE                                                                                           */
    /* 5  B   D JACK LEON JUNE JILL                                                                                           */
    /* 6  C   D MARK LEON ROSE JILL                                                                                           */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*  _                                      _   _                             _
    | || |   __      ___ __  ___   _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
    | || |_  \ \ /\ / / `_ \/ __| | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
    |__   _|  \ V  V /| |_) \__ \ | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
       |_|     \_/\_/ | .__/|___/ | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
                      |_|         |_|    |___/                                |_|
    */


    %utlfkil(d:/xpt/want.xpt);

    /*----                                                                   ----*/
    /*----  elimiate issues with datastep and view with the same name        ----*/
    /*----                                                                   ----*/

    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;
    proc datasets lib=work nolist mt=data mt=view nodetails;delete want; run;quit;

    %utl_submit_wps64x("
    options validvarname=any lrecl=32756;
    libname sd1 'd:/sd1';
    proc python;
    export data=sd1.have  python=have;
    submit;
    import pyreadstat as ps;
    from os import path;
    import pandas as pd;
    import numpy as np;
    from pandasql import sqldf;
    mysql = lambda q: sqldf(q, globals());
    from pandasql import PandaSQL;
    pdsql = PandaSQL(persist=True);
    sqlite3conn = next(pdsql.conn.gen).connection.connection;
    sqlite3conn.enable_load_extension(True);
    sqlite3conn.load_extension('c:/temp/libsqlitefunctions.dll');
    mysql = lambda q: sqldf(q, globals());
    want = pdsql('''
      select
         l.id as id
        ,r.id as id1
        ,l.a
        ,r.a  as a1
        ,l.b
        ,r.b  as b1
      from
        have as l left join have as r
      on
        l.id < r.id
      where
        r.id is not null
      order
        by l.id
    ''');
    print(want);
    ps.write_xport(want,'d:\\xpt\\want.xpt',table_name='want',file_format_version=5
    ,column_labels=['ID' ,'ID1' ,'A' ,'A1' ,'B' ,'B1']);
    endsubmit;
    ");

    /*--- handles long variable names by using the label to rename the variables  ----*/

    libname xpt xport "d:/xpt/want.xpt";
    proc contents data=xpt._all_;
    run;quit;

    data want_py_long_names;
      %utl_rens(xpt.want) ;
      set want;
    run;quit;
    libname xpt clear;

    proc print data=want_py_long_names;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  WPS PYTHON Procedure                                                                                                  */
    /*                                                                                                                        */
    /*   ID       ID1         A        A1         B        B1                                                                 */
    /*                                                                                                                        */
    /*   A         B         MIKE      JACK      MARY      JUNE                                                               */
    /*   A         C         MIKE      MARK      MARY      ROSE                                                               */
    /*   A         D         MIKE      LEON      MARY      JILL                                                               */
    /*   B         C         JACK      MARK      JUNE      ROSE                                                               */
    /*   B         D         JACK      LEON      JUNE      JILL                                                               */
    /*   C         D         MARK      LEON      ROSE      JILL                                                               */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___
    | ___|   _ __ ___ _ __   ___  ___
    |___ \  | `__/ _ \ `_ \ / _ \/ __|
     ___) | | | |  __/ |_) | (_) \__ \
    |____/  |_|  \___| .__/ \___/|___/
                     |_|
    */

    https://github.com/rogerjdeangelis/utl-passing-arguments-to-sqldf-wps-r-sql-functional-sql

    https://github.com/rogerjdeangelis/utl-adding-the-missing-math-stat-and-string-functions-to-python-sql-pandasql-sqllite3
    https://github.com/rogerjdeangelis/utl-exporting-python-panda-dataframes-to-wps-r-using-a-shared-sqllite-database
    https://github.com/rogerjdeangelis/utl-analyzing-mean-and-median-by-groups-in-sas-wps-r-python
    https://github.com/rogerjdeangelis/utl-calculate-percentage-by-group-in-wps-r-python-excel-sql-no-sql
    https://github.com/rogerjdeangelis/utl-cartesian-join-with-condition-in-sql-wps-r-python
    https://github.com/rogerjdeangelis/utl-create-new-column-based-on-complex-logic-involving-four-other-columns-in-wps-r-python-sql-nosql
    https://github.com/rogerjdeangelis/utl-efficiently-swap-values-between-related-columns-in-wps-r-python-sql-nosql
    https://github.com/rogerjdeangelis/utl-frequency-of-duplicated-digits-in-social-security-numbers-in-wps-r-python-sql
    https://github.com/rogerjdeangelis/utl-how-to-compare-one-set-of-columns-with-another-set-of-columns-wps-r-python-sql
    https://github.com/rogerjdeangelis/utl-insert-four-rows-after-each-row-so-each-new-row-is-one-hour-later-wps-r-python-datetimes
    https://github.com/rogerjdeangelis/utl-leveraging-your-knowledge-of-perl-regex-to-sas-wps-r-python-and-perl
    https://github.com/rogerjdeangelis/utl-merging-inner-join-dataframes-based-on-single-primary-key-in-wps-r-python-sql-nosql
    https://github.com/rogerjdeangelis/utl-pivot-transpose-by-id-using-wps-r-python-sql-using-partitioning
    https://github.com/rogerjdeangelis/utl-rename-and-cast-char-to-numeric-using-do-over-arrays-in-natve-and-mysql-wps-r-python
    https://github.com/rogerjdeangelis/utl-sas-proc-transpose-wide-to-long-in-sas-wps-r-python-native-and-sql
    https://github.com/rogerjdeangelis/utl-select-all-unique-pairs-of-ingredients-in-salads-r-wps-r-python-sql
    https://github.com/rogerjdeangelis/utl-select-groups-of-rows-having-a-compound-condition-and-further-subset-using-wps-r-python-sql
    https://github.com/rogerjdeangelis/utl-select-students-that-have-not-transfered-schools-over-the-last-three-years-wps-r-python-sql
    https://github.com/rogerjdeangelis/utl-select-the-first-two-observations-from-each-group-wps-r-python-sql
    https://github.com/rogerjdeangelis/utl-set-type-for-subject-based-on-baseline-dose-wps-r-python-sql
    https://github.com/rogerjdeangelis/utl-top-four-seasonal-precipitation-totals--european-cities-sql-partitions-in-wps-r-python
    https://github.com/rogerjdeangelis/utl-transpose-fat-to-skinny-pivot-longer-in-sas-wps-r-pythonv
    https://github.com/rogerjdeangelis/utl-transpose-pivot-wide-using-sql-partitioning-in-wps-r-python
    https://github.com/rogerjdeangelis/utl-tumbling-goups-of-ten-temperatures-similar-like-rolling-and-moving-means-wps-r-python
    https://github.com/rogerjdeangelis/utl-using-nonlinear-optimization-to-fit-an-arc-tangent-curve-wps-r-python
    https://github.com/rogerjdeangelis/utl-using-sql-in-wps-r-python-select-the-four-youngest-male-and-female-students-partitioning


    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
