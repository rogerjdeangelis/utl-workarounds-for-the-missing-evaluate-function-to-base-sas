# utl-workarounds-for-the-missing-evaluate-function-to-base-sas
Possible Workaround for the missing evaluate function to base sas
    Possible Workaround for the missing evaluate function to base sas.

    github
    https://tinyurl.com/yamqwvw4
    https://github.com/rogerjdeangelis/utl-workarounds-for-the-missing-evaluate-function-to-base-sas

    PERL, R and Python have expression evaluators

    Q's SAS Global Forum Paper helped me with this solution.
    Note the global option on 'call symputx'.

    Please experiment with dosubl.

       Two Solutions
             a. DOSUBL compile
             b. DOSUBL pass code
             c. Barts HASH SPDE  (very nice eye opening use of SAS functionality)
                https://tinyurl.com/y7rsb7hs  (more general solution)
                Bartosz Jablonski
                yabwon@gmail.com
             d. Fried Eggs LUA
                FriedEgg <00000a7c04fef931-dmarc-request@listserv.uga.edu>
                Requires stricter typing
             e. R
                Input is slightly different (see below)
                You need to map the operators in 'code' string to R and Python
             f. Python (0ne liner)
                x=3;
                y=11;
                print(eval('x>2 and y<10'));
                > False
             g. PERL has an eval function - not shown.
    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    * use this input, the spacing is important.
      I am not interested in writing a general compiler;


    data have;
     informat code $50.;
     input  x y c$ code &;
    cards4;
    +3 11 A ( x > 1) and ( c ="A")
    -3 17 B ( x < 1) and (9 < y < 13)
    +3 11 C ( x < 1) or ( c in ("C","D","E"))
    -3 11 D ( y < 12)
    -3 11 D 1
    ;;;;
    run;quit;

    *           _
     _ __ _   _| | ___  ___
    | '__| | | | |/ _ \/ __|
    | |  | |_| | |  __/\__ \
    |_|   \__,_|_|\___||___/

    ;                                                    | RULES
                                                         |
       X     Y    C    SAVE_CODE                         |   CODE_SUBSTITUTION                 TRUTH
                                                         |
       3    11    A    ( x > 1) and ( c ="A")            | ( 3 > 1) and ("A"="A")                1
      -3    17    B    ( x < 1) and (9 < y < 13)         | (-3 < 1) and (9 <17 < 13)             0
       3    11    C    ( x < 1) or ( c in ("C","D","E")) | ( 3 < 1) or ("C"in ("C","D","E"))     1
      -3    11    D    ( y < 12)                         | (11 < 12)                             1
      -3    11    D    1                                 | 1                                     1

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|                  _ _
      __ _      ___ ___  _ __ ___  _ __ (_) | ___
     / _` |    / __/ _ \| '_ ` _ \| '_ \| | |/ _ \
    | (_| |_  | (_| (_) | | | | | | |_) | | |  __/
     \__,_(_)  \___\___/|_| |_| |_| .__/|_|_|\___|
                                  |_|
    ;

    WORK.WANT total obs=5

      X     Y    C    SAVE_CODE                           TRUTH   CODE_SUBSTITUTION

      3    11    A    ( x > 1) and ( c ="A")                1     ( 3 > 1) and ("A"="A")
     -3    17    B    ( x < 1) and (9 < y < 13)             0     (-3 < 1) and (9 <17 < 13)
      3    11    C    ( x < 1) or ( c in ("C","D","E"))     1     ( 3 < 1) or ("C"in ("C","D","E"))
     -3    11    D    ( y < 12)                             1     (11 < 12)
     -3    11    D    1                                     1     1

    *_                                            _
    | |__     _ __   __ _ ___ ___    ___ ___   __| | ___
    | '_ \   | '_ \ / _` / __/ __|  / __/ _ \ / _` |/ _ \
    | |_) |  | |_) | (_| \__ \__ \ | (_| (_) | (_| |  __/
    |_.__(_) | .__/ \__,_|___/___/  \___\___/ \__,_|\___|
             |_|
    ;

    WORK.WANT total obs=5

      CODE                                  X     Y    C    RC    TRUTH

      ( x > 1) and ( c ="A")                3    11    A     0      1
      ( x < 1) and (9 < y < 13)            -3    17    B     0      0
      ( x < 1) or ( c in ("C","D","E"))     3    11    C     0      0
      ( y < 12)                            -3    11    D     0      1
      1                                    -3    11    D     0      1

    *         _               _
      ___    | |__   __ _ ___| |__
     / __|   | '_ \ / _` / __| '_ \
    | (__ _  | | | | (_| \__ \ | | |
     \___(_) |_| |_|\__,_|___/_| |_|

    ;

    https://tinyurl.com/y7rsb7hs
    https://listserv.uga.edu/cgi-bin/wa?A2=ind2004c&L=SAS-L&O=D&X=C9AADC0D47FA1D0400&Y=rogerjdeangelis%40gmail.com&P=7061

     WORKWANT total obs=4

     The hash outputs the observations that are logically true

     Obs    CODE                                X     Y    C    STRX

      1     ( x > 1) and ( c ="A")              3    11    A    WRK.HAVE(startobs = 1   endobs = 1    where = (( x > 1) and ( c ="A")))
      2     ( x < 1) or ( c in ("C","D","E"))   3    11    C    WRK.HAVE(startobs = 3   endobs = 3    where = (( x < 1) or ( c in ("C","D","E"))))
      3     ( y < 12)                          -3    11    D    WRK.HAVE(startobs = 4   endobs = 4    where = (( y < 12)))
      4     1                                  -3    11    D    WRK.HAVE(startobs = 5   endobs = 5    where = (1))

    *    _     _
      __| |   | |_   _  __ _
     / _` |   | | | | |/ _` |
    | (_| |_  | | |_| | (_| |
     \__,_(_) |_|\__,_|\__,_|

    ;

    Strongly typed language - not always a bad idea;

    * note the '==' (nneded on input' see solution below.

    Up to 40 obs from TEST total obs=4

    Obs     X     Y    C    CODE

     1      3    11    A    (x > 1) and c=='A'
     3      3    11    C    (x < 1) or c=="C" or c=="D" or c=="E"
     4     -3    11    D    (y < 12)
     5     -3    11    D    1

    *         ____
      ___    |  _ \
     / _ \   | |_) |
    |  __/_  |  _ <
     \___(_) |_| \_\

    ;

     WORK.WANT total obs=1

           CODE         X     Y    C     Z

       X > 2 & Y >10    3    11    A    TRUE

    *____  ____   ___   ____ _____ ____ ____
    |  _ \|  _ \ / _ \ / ___| ____/ ___/ ___|
    | |_) | |_) | | | | |   |  _| \___ \___ \
    |  __/|  _ <| |_| | |___| |___ ___) |__) |
    |_|   |_| \_\\___/ \____|_____|____/____/

                                         _ _
      __ _      ___ ___  _ __ ___  _ __ (_) | ___
     / _` |    / __/ _ \| '_ ` _ \| '_ \| | |/ _ \
    | (_| |_  | (_| (_) | | | | | | |_) | | |  __/
     \__,_(_)  \___\___/|_| |_| |_| .__/|_|_|\___|
                                  |_|
    ;

    data want;

      set have;
       save_code=code;
       substr(code,index(code,'x')-1,2)=put(x,2.);
       substr(code,index(code,'y')-1,2)=put(y,2.);
       substr(code,index(code,'c')-1,3)=cats('"',c,'"');
       call symputx('code',code);
       rc=dosubl('
             data _null_;
                  call symputx("res",&code,"G");
             run;quit;
            ');

        truth=symgetn('res');

    run;quit;
    *_                                            _
    | |__     _ __   __ _ ___ ___    ___ ___   __| | ___
    | '_ \   | '_ \ / _` / __/ __|  / __/ _ \ / _` |/ _ \
    | |_) |  | |_) | (_| \__ \__ \ | (_| (_) | (_| |  __/
    |_.__(_) | .__/ \__,_|___/___/  \___\___/ \__,_|\___|
             |_|
    ;

    %let beenthere=1;
    data want;

      set have;

       call symputx('code',code);

       rc=dosubl('
             data _null_;
                  set have(firstobs=&beenthere obs=&beenthere);
                  call symputx("res",&code,"G");
             run;quit;
             %let beeenthere=%eval(&beenthere + 1);
            ');

        truth=symgetn('res');

    run;quit;

    *         _               _
      ___    | |__   __ _ ___| |__
     / __|   | '_ \ / _` / __| '_ \
    | (__ _  | | | | (_| \__ \ | | |
     \___(_) |_| |_|\__,_|___/_| |_|

    ;

    * input is same as above bur using spde;
    options dlcreatedir;
    libname wrk "%sysfunc(pathname(work))\spde\";
    libname wrk SPDE "%sysfunc(pathname(work))\spde\";

    data wrk.have;
     informat code $44.;
     input  x y c$ code &;
    cards4;
    +3 11 A ( x > 1) and ( c ="A")
    -3 17 B ( x < 1) and (9 < y < 13)
    +3 11 C ( x < 1) or ( c in ("C","D","E"))
    -3 11 D ( y < 12)
    -3 11 D 1
    ;;;;
    run;quit;


    /* options nonotes; */ /* remove comment for clean log */
    data want;
      set
        wrk.have
      curobs=curobs indsname=indsname end=end
      ;

      if missing(code) then
        do;
          output;
        end;
      else
        do;
          length strX $ 1024;
          strX = cat(
            strip(indsname)
            , '(startobs = ', curobs           /* <-- */
            , '   endobs = ', curobs           /* <-- */
            , '    where = (', strip(code), '))'
            );
          declare hash H(dataset:strX);
          rc = H.defineKey("code");
          rc = H.defineDone();
          if H.num_items > 0 then output;
          rc = H.delete();
        end;

      if end then put _N_=;
    run;

    *    _     _
      __| |   | |_   _  __ _
     / _` |   | | | | |/ _` |
    | (_| |_  | | |_| | (_| |
     \__,_(_) |_|\__,_|\__,_|

    ;

    * requires double equals (like Python and R:
    data test;
    length x y 8 c $ 10 code $ 50;
    code = '(x > 1) and c==''A''';           x= 3; y=11; c="A"; output;
    code = '(x < 1 and 9 < y and y < 13)';      x=-3; y=17; c="B"; output;
    code = '(x < 1) or c=="C" or c=="D" or c=="E"'; x= 3; y=11; c="C"; output;
    code = '(y < 12)';                       x=-3; y=11; c="D"; output;
    code = '1';                       x=-3; y=11; c="D"; output;
    run;

    proc lua;
    submit;
      local dsid = sas.open('work.test','u')
      while sas.next(dsid) do
        local codeString = sas.get_value(dsid,'code')
          x = sas.get_value(dsid,'x')
          y = sas.get_value(dsid,'y')
          c = sas.get_value(dsid,'c')
          local codeEval = load('return ' .. codeString)
          if not codeEval() then
            sas.delobs(dsid)
          end
      end
      sas.close(dsid)
    endsubmit;
    run;

    *         ____
      ___    |  _ \
     / _ \   | |_) |
    |  __/_  |  _ <
     \___(_) |_| \_\

    ;

    libname sd1 "d:/sd1";

    data sd1.have;
     informat code $44.;
     input  x y c$ code &;
    cards4;
    +3 11 A X > 2 & Y > 10
    ;;;;
    run;quit;


    %utl_submit_r64('
    library(dplyr);
    library(haven);
    library(SASxport);
    library(data.table);
    have<-read_sas("d:/sd1/have.sas7bdat");
    g = function(df, s){
        q = quote(mutate(df, z = s));
        eval(parse(text=sub("s", s, deparse(q))));
    };
    want<-as.data.table(g(have, have$CODE));
    want;
    write.xport(want,file="d:/xpt/eval_r.xpt");
    ');

    libname xpt xport "d:/xpt/eval_r.xpt";
    data want;
      set xpt.want;
    run;quit;
    libname xpt clear;

    * __                _   _
     / _|   _ __  _   _| |_| |__   ___  _ __
    | |_   | '_ \| | | | __| '_ \ / _ \| '_ \
    |  _|  | |_) | |_| | |_| | | | (_) | | | |
    |_|(_) | .__/ \__, |\__|_| |_|\___/|_| |_|
           |_|    |___/
    ;


    %utl_submit_py64_38("
    x=3;
    y=11;
    print(eval('x>2 and y<10'));
    ");

    > False


