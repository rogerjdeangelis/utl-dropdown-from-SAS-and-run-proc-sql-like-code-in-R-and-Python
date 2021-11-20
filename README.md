# utl-dropdown-from-SAS-and-run-proc-sql-like-code-in-R-and-Python
Dropdown from SAS and run proc sql like code in R and Python
    %let pgm=utl-dropdown-from-SAS-and-run-proc-sql-like-code-in-R-and-Python;

    Dropdown from SAS and run proc sql like code in R and Python

    github
    https://tinyurl.com/x7ukyymf
    https://github.com/rogerjdeangelis/utl-dropdown-from-SAS-and-run-proc-sql-like-code-in-R-and-Python

    options validvarname=upcase;

    libname sd1 "d:/sd1";

    data sd1.havOne;
       input School $ count ;
    cards4;
    Comm 42
    IR 52
    Business 34
    Nursing 23
    ;;;;
    run;quit;

    data sd1.havTwo;
     input School $ uniques;
    cards4;
    Comm 17
    IR 18
    Business 14
    Nursing 12
    ;;;;
    run;quit;

                            |                      |
         TABLE SD1.HAVONE   |    TABLE SD1.HAVTWO  |  MAKE THIS TABLE
                            |                      |
        SCHOOL       COUNT  |   SCHOOL     UNIQUES |   SCHOOL    COUNT  UNIQUE
                            |                      |
        Comm          42    |   Comm          17   |   Comm       42      17
        IR            52    |   IR            18   |   IR         52      18
        Business      34    |   Business      14   |   Business   34      14
        Nursing       23    |   Nursing       12   |   Nursing    23      12
                            |                      |
    /*___
    |  _ \
    | |_) |
    |  _ <
    |_| \_\

    */

    * MAKE SURE YOUR FOLDER IS OR FILE IS NOT READONLY;

    * remove read-only attribute - may not need to do this - I did?;
    x "attrib -R d:/xpt/py_want.xpt";

    %utlfkil(d:/xpt/r_want.xpt);

    %utl_submit_r64('
    library(sqldf);
    library(haven);
    library(SASxport);
    havone<-read_sas("d:/sd1/havone.sas7bdat");
    havtwo<-read_sas("d:/sd1/havtwo.sas7bdat");
    want<-sqldf("
     select
         l.SCHOOL
        ,l.COUNT
        ,r.UNIQUES
     from
         havone as l, havtwo as r
     where
         l.SCHOOL = R.SCHOOL
    ");
    want;
    str(want);
    write.xport(want,file="d:/xpt/r_want.xpt");
    ');

    libname xpt xport "d:/xpt/r_want.xpt";
    proc print data=xpt.want;
    run;quit;

    proc contents data=xpt.want;
    run;quit;

    /*           _   _
     _ __  _   _| |_| |__   ___  _ __
    | `_ \| | | | __| `_ \ / _ \| `_ \
    | |_) | |_| | |_| | | | (_) | | | |
    | .__/ \__, |\__|_| |_|\___/|_| |_|
    |_|    |___/
    */


    * remove read-only attribute - may not need to do this - I did?;
    x "attrib -R d:/xpt/py_want.xpt";

    * delete file;
    %utlfkil(d:\xpt\py_want.xpt);
    run;quit;

    %utl_submit_py64_38("
    import pyreadstat;
    import pandas as pd;
    from pandasql import sqldf, load_meat, load_births;
    pysqldf = lambda q: sqldf(q, globals());
    havone, mhavone = pyreadstat.read_sas7bdat('d:/sd1/havone.sas7bdat');
    havtwo, mhavtwo = pyreadstat.read_sas7bdat('d:/sd1/havtwo.sas7bdat');
    want=pysqldf('select l.SCHOOL,l.COUNT,r.UNIQUES from havone l inner join havtwo as r on l.SCHOOL = r.SCHOOL');
    print(want);
    pyreadstat.write_xport(want, 'd:/xpt/py_want.xpt',table_name='want');
    ");

    libname xpt xport "d:/xpt/py_want.xpt";

    proc print data=xpt.want;
    run;quit;

    proc contents data=xpt.want;
    run;quit;


    /*           _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| `_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    */

      Obs    SCHOOL      COUNT    UNIQUES

        1    Comm          42        17
        2    IR            52        18
        3    Business      34        14
        4    Nursing       23        12


    The CONTENTS Procedure

    Data Set Name        XPT.WANT                  Observations          .
    Member Type          DATA                      Variables             3
    Engine               XPORT                     Indexes               0
    Created              11/20/2021 09:44:18       Observation Length    24
    Last Modified        11/20/2021 09:44:18       Deleted Observations  0
    Protection                                     Compressed            NO
    Data Set Type                                  Sorted                NO
    Label
    Data Representation  Default
    Encoding             Default


    Alphabetic List of Variables and Attributes

    #    Variable    Type    Len

    2    COUNT       Num       8
    1    SCHOOL      Char      8
    3    UNIQUES     Num       8

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
