# utl-join-two-ables-based-on-predefined-meta-data-rules
Join two tables based on predefined meta data rules
    %let pgm=utl-join-two-ables-based-on-predefined-meta-data-rules;

    Join two tables based on predefined meta data rules

    https://github.com/rogerjdeangelis/utl-join-two-ables-based-on-predefined-meta-data-rules

    https://stackoverflow.com/questions/77230754/how-to-merge-these-two-dataframes

          SOLUTIONS

             1 wps datastep hardcoded
             2 wps datastep dynamic (rules in macro code - makes sense or config table?)
             3 wps base r
    /*                   _
    (_)_ __  _ __  _   _| |_ ___
    | | `_ \| `_ \| | | | __/ __|
    | | | | | |_) | |_| | |_\__ \
    |_|_| |_| .__/ \__,_|\__|___/
            |_|
    */

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.master;
      informat code $1. periode $10.  prix $1.;
      input code periode prix;
    cards4;
    a D20230101 3
    b D20230101 2
    c D20230201 2
    d D20230201 4
    ;;;;
    run;quit;

    data sd1.transaction;
    retain
    D20220201
    D20210201
    D20200201
    D20220101
    D20210101
    D20200101
    ;
    input code $1. D20180101 D20180201 D20190101 D20190201 D20200101 D20200201 D20210101 D20210201 D20220101 D20220201;
    drop D20180101 D20180201 D20190101 D20190201;
    cards4;
    a 2 1 2 3 3 4 1 2 2 1
    b 1 2 4 2 1 2 1 5 1 2
    c 2 1 5 6 1 3 4 3 1 3
    d 3 2 7 8 6 4 5 9 7 8
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*  _                   _                                                                                                 */
    /* (_)_ __  _ __  _   _| |_ ___                                                                                           */
    /* | | `_ \| `_ \| | | | __/ __|                                                                                          */
    /* | | | | | |_) | |_| | |_\__ \                                                                                          */
    /* |_|_| |_| .__/ \__,_|\__|___/                                                                                          */
    /*         |_|                                                                                                            */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /* Obs    CODE    PERIODE      PRIX                                                                                       */
    /*                                                                                                                        */
    /*  1      a      D20230101     3                                                                                         */
    /*  2      b      D20230101     2                                                                                         */
    /*  3      c      D20230201     2                                                                                         */
    /*  4      d      D20230201     4                                                                                         */
    /*                                               Here where get for rows a and b in output                                */
    /*                                                    N_1          N_2          N_3                                       */
    /*                                                                                                                        */
    /*   CODE    D20200101    D20210101    D20220101    D20200201    D20210201    D20220201                                   */
    /*                                                                                                                        */
    /*    a          2            4            2            3            1            2                                       */
    /*    b          1            2            5            1            1            1                                       */
    /*                                                                                                                        */
    /*                           Here where get for rows c and d in output                                                    */
    /*                           N_1          N_2          N_3                                                                */
    /*                                                                                                                        */
    /*    c          2            3            3            1            4            1                                       */
    /*    d          3            4            9            6            5            7                                       */                                                                                   */
    /*                 _                     _                                                                                */
    /*  _ __ ___   ___| |_ __ _   _ __ _   _| | ___  ___                                                                      */
    /* | `_ ` _ \ / _ \ __/ _` | | `__| | | | |/ _ \/ __|                                                                     */
    /* | | | | | |  __/ || (_| | | |  | |_| | |  __/\__ \                                                                     */
    /* |_| |_| |_|\___|\__\__,_| |_|   \__,_|_|\___||___/                                                                     */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /*  =============== OUTPUT ===============  | ==============  RULES ====================================================  */
    /*  CODE   PERIODE   PRIX   N_1   N_2   N_3 |                                                      Column         previos */
    /*                                          |                                                      in Transct     years   */
    /*                                          |                                                                             */
    /* 1 a     20230101     3     2     1     3 | code=a and  periode = D20230101 in master then N_1 = D20220101 = 2  2022    */
    /*                                          |                                                N_2 = D20210101 = 1  2021    */
    /*                                          |                                                N_3 = D20200101 = 3  2020    */
    /*                                          |                                                                             */
    /* 2 b     20230101     2     1     1     1 | code=b and  periode = D20230101 in master then N_1 = D20220101 = 1          */
    /*                                          |                                                N_2 = D20210101 = 1          */
    /*                                          |                                                N_3 = D20200101 = 1          */
    /*                                          |                                                                             */
    /* 3 c     20230201     2     3     3     3 | code=c and  periode = D20230201 in master then N_1 = D20220201 = 3          */
    /*                                          |                                                N_2 = D20210201 = 3          */
    /*                                          |                                                N_3 = D20200201 = 3          */
    /*                                          |                                                                             */
    /*                                          |                                                                             */
    /* 4 d     20230201     4     8     9     4 | code=d and  periode = D20230201 in master then N_1 = D20220201 = 8          */
    /*                                          |                                                N_2 = D20210201 = 9          */
    /*                                          |                                                N_3 = D20200201 = 4          */
    /*              _               _           |                                                                             */
    /*   ___  _   _| |_ _ __  _   _| |_         |                                                                             */
    /*  / _ \| | | | __| `_ \| | | | __|                                                                                      */
    /* | (_) | |_| | |_| |_) | |_| | |_                                                                                       */
    /*  \___/ \__,_|\__| .__/ \__,_|\__|                                                                                      */
    /*                 |_|                                                                                                    */
    /*                                                                                                                        */
    /*   CODE     PERIODE     PRIX    N_1    N_2    N_3                                                                       */
    /*                                                                                                                        */
    /*    a      D20230101     3       2      1      3                                                                        */
    /*    b      D20230101     2       1      1      1                                                                        */
    /*    c      D20230201     2       3      3      3                                                                        */
    /*    d      D20230201     4       8      9      4                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*                            _       _            _              _                   _               _          _
    / | __      ___ __  ___    __| | __ _| |_ __ _ ___| |_ ___ _ __  | |__   __ _ _ __ __| | ___ ___   __| | ___  __| |
    | | \ \ /\ / / `_ \/ __|  / _` |/ _` | __/ _` / __| __/ _ \ `_ \ | `_ \ / _` | `__/ _` |/ __/ _ \ / _` |/ _ \/ _` |
    | |  \ V  V /| |_) \__ \ | (_| | (_| | || (_| \__ \ ||  __/ |_) || | | | (_| | | | (_| | (_| (_) | (_| |  __/ (_| |
    |_|   \_/\_/ | .__/|___/  \__,_|\__,_|\__\__,_|___/\__\___| .__/ |_| |_|\__,_|_|  \__,_|\___\___/ \__,_|\___|\__,_|
                 |_|                                          |_|
    */

    proc datasets lib=sd1 nolist nodetails;delete want; run;quit;

    %utl_submit_wps64x('

    libname sd1 "d:/sd1";
    data sd1.want;

      merge sd1.master sd1.transaction;
      by code;

      select;
        when ( periode = "D20230101") do;
                   n_1 = D20220101;
                   n_2 = D20210101;
                   n_3 = D20200101;
        end;
        when ( periode = "D20230201") do;
                   n_1 = D20220201;
                   n_2 = D20210201;
                   n_3 = D20200201;
        end;
        otherwise;
      end;
      drop d:;
    run;quit;

    proc print data=sd1.want;
    run;quit;
    ');

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* The WPS System                                                                                                         */
    /*                                                                                                                        */
    /* Obs    CODE     PERIODE     PRIX    N_1    N_2    N_3                                                                  */
    /*                                                                                                                        */
    /*  1      a      D20230101     3       2      1      3                                                                   */
    /*  2      b      D20230101     2       1      1      1                                                                   */
    /*  3      c      D20230201     2       3      3      3                                                                   */
    /*  4      d      D20230201     4       8      9      4                                                                   */
    /*                                                                                                                        */
    /**************************************************************************************************************************/


    /*                        _       _            _                  _                             _
    __      ___ __  ___    __| | __ _| |_ __ _ ___| |_ ___ _ __    __| |_   _ _ __   __ _ _ __ ___ (_) ___
    \ \ /\ / / `_ \/ __|  / _` |/ _` | __/ _` / __| __/ _ \ `_ \  / _` | | | | `_ \ / _` | `_ ` _ \| |/ __|
     \ V  V /| |_) \__ \ | (_| | (_| | || (_| \__ \ ||  __/ |_) || (_| | |_| | | | | (_| | | | | | | | (__
      \_/\_/ | .__/|___/  \__,_|\__,_|\__\__,_|___/\__\___| .__/  \__,_|\__, |_| |_|\__,_|_| |_| |_|_|\___|
             |_|                                          |_|           |___/
    */

    proc datasets lib=sd1 nolist nodetails;delete want; run;quit;

    %utl_submit_wps64x('

    %macro utl_mrgRules(curYer=2023,months=2,preYrs=3)/des="define the ruls for cururent and previous years";

      %local mon yerIdx pre pad0;

      %do mon =1 %to &months ;

           %let pad0 = %sysfunc(putn(&mon,z2.));

           when ( periode = "D&curYer.&pad0.01") do;

        %do yerIdx = 1 %to &preYrs;

            %let pre = %eval(&curYer - &yerIdx);

                      n_&yerIdx = D&pre.&pad0.01;

        %end;

        end;

      %end;

    %mend utl_mrgRules;

    libname sd1 "d:/sd1";

    data sd1.want;

      merge sd1.master sd1.transaction;
      by code;

      select;

        %utl_mrgRules(curYer=2023,months=2,preYrs=3)

        otherwise;

      end;

      drop d:;

    run;quit;

    proc print data=sd1.want;
    run;quit;
    ');

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*  GENERATED RULES (output of macro)                                                                                     */
    /*                                                                                                                        */
    /*  when ( periode = "D20230101") do;                                                                                     */
    /*  n_1 = D20220101;                                                                                                      */
    /*  n_2 = D20210101;                                                                                                      */
    /*  n_3 = D20200101;                                                                                                      */
    /*  end;                                                                                                                  */
    /*  when ( periode = "D20230201") do;                                                                                     */
    /*  n_1 = D20220201;                                                                                                      */
    /*  n_2 = D20210201;                                                                                                      */
    /*  n_3 = D20200201;                                                                                                      */
    /*  end;                                                                                                                  */
    /*  otherwise;                                                                                                            */
    /*  end;                                                                                                                  */
    /*                                                                                                                        */
    /* The WPS System                                                                                                         */
    /*                                                                                                                        */
    /* Obs    CODE     PERIODE     PRIX    N_1    N_2    N_3                                                                  */
    /*                                                                                                                        */
    /*  1      a      D20230101     3       2      1      3                                                                   */
    /*  2      b      D20230101     2       1      1      1                                                                   */
    /*  3      c      D20230201     2       3      3      3                                                                   */
    /*  4      d      D20230201     4       8      9      4                                                                   */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*____                        _
    |___ /  __      ___ __  ___  | |__   __ _ ___  ___   _ __
      |_ \  \ \ /\ / / `_ \/ __| | `_ \ / _` / __|/ _ \ | `__|
     ___) |  \ V  V /| |_) \__ \ | |_) | (_| \__ \  __/ | |
    |____/    \_/\_/ | .__/|___/ |_.__/ \__,_|___/\___| |_|
                     |_|
    */

    proc datasets lib=sd1 nolist nodetails;delete want; run;quit;

    %utl_submit_wps64x('
    libname sd1 "d:/sd1";
    proc r;
    submit;
    library(tidyr);
    library(dplyr);

     tab1 <- tribble(
      ~code, ~periode, ~prix,
      "a", "2023-01-01", 3,
      "b", "2023-01-01", 2,
      "c", "2023-02-01", 2,
      "d", "2023-02-01", 4
    );

    tab2 <- tribble(
      ~code, ~`2018-01-01`,  ~`2018-02-01`,  ~`2019-01-01`, ~`2019-02-01`, ~`2020-01-01`,
      ~`2020-02-01`,~`2021-01-01`,  ~`2021-02-01`, ~`2022-01-01`, ~`2022-02-01`,
      "a", 2, 1, 2, 3, 3, 4, 1, 2, 2, 1,
      "b", 1, 2, 4, 2, 1, 2, 1, 5, 1, 2,
      "c", 2, 1, 5, 6, 1, 3, 4, 3, 1, 3,
      "d", 3, 2, 7, 8, 6, 4, 5, 9, 7, 8
    );

    df2 <- tab2 %>%
      pivot_longer(-1) %>%
      mutate(name=as.Date(name));

    want<-tab1 %>%
      mutate(periode2=purrr::map(as.Date(periode),seq,by="-1 year",length.out=4))%>%
      unnest(periode2) %>%
      left_join(df2,join_by(code,closest(periode2>=name))) %>%
      mutate(id=row_number()-1,.by=code) %>%
      filter(id!=0)%>%
      select(-name,-periode2)%>%
      pivot_wider(names_from=id,names_prefix = "N_");

    want;

    endsubmit;
    import data=sd1.want r=want;
    run;quit;
    ');

    proc print data=sd1.want;
    run;quit;


    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* WPS Proc R                                                                                                             */
    /*                                                                                                                        */
    /* # A tibble: 4 x 6                                                                                                      */
    /*   code  periode     prix   N_1   N_2   N_3                                                                             */
    /*   <chr> <chr>      <dbl> <dbl> <dbl> <dbl>                                                                             */
    /* 1 a     2023-01-01     3     2     1     3                                                                             */
    /* 2 b     2023-01-01     2     1     1     1                                                                             */
    /* 3 c     2023-02-01     2     3     3     3                                                                             */
    /* 4 d     2023-02-01     4     8     9     4                                                                             */
    /*                                                                                                                        */
    /* The WPS System                                                                                                         */
    /*                                                                                                                        */
    /* Obs    CODE     PERIODE     PRIX    N_1    N_2    N_3                                                                  */
    /*                                                                                                                        */
    /*  1      a      D20230101     3       2      1      3                                                                   */
    /*  2      b      D20230101     2       1      1      1                                                                   */
    /*  3      c      D20230201     2       3      3      3                                                                   */
    /*  4      d      D20230201     4       8      9      4                                                                   */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
