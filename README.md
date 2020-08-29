# utl_ods-output-datasets-for-a-very-complex-proc-tabulate
Ods output datasets for a very complex proc tabulate 
    Ods output datasets for a very complex proc tabulate                                                                                                                                            
    As always works best with the classic SAS 1980s Editor.                                                                                                                                         
                                                                                                                                                                                                    
      Op wants a sas dataset from proc tabulate.                                                                                                                                                    
                                                                                                                                                                                                    
      github                                                                                                                                                                                        
      https://tinyurl.com/y5fog6kq                                                                                                                                                                  
      https://github.com/rogerjdeangelis/utl_ods-output-datasets-for-a-very-complex-proc-tabulate                                                                                                   
                                                                                                                                                                                                    
      Related to                                                                                                                                                                                    
      see this post for atypical problem with tabulate (painting yourself into a corner)                                                                                                            
      https://tinyurl.com/y6juu9zx                                                                                                                                                                  
      https://communities.sas.com/t5/SAS-Programming/Convert-proc-tabulate-to-proc-report-and-show-each-value-of/m-p/679656                                                                         
                                                                                                                                                                                                    
      macros                                                                                                                                                                                        
      https://tinyurl.com/y9nfugth                                                                                                                                                                  
      https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories                                                                                                    
                                                                                                                                                                                                    
      Five Solutions                                                                                                                                                                                
                                                                                                                                                                                                    
             a.   Using 'proc report' and macro utl_odsrpt instead of 'proc tabulate'                                                                                                               
                  Requires minor pre or post processing to get a fes od the summary columns                                                                                                         
                  However I suggest you do not use the _c##_ variables to get the partal sums.                                                                                                      
                  Either condition the input or post process using the outot table to get the sums.                                                                                                 
                  My utl_odsrpt with a large pasesize ir pagesize=5000 should work. (max width 384)                                                                                                 
                                                                                                                                                                                                    
                     Process                                                                                                                                                                        
                                                                                                                                                                                                    
                         Using the macro utl_odsrpt (on end of message and in github)                                                                                                               
                         This involves sending the printed output to a file and                                                                                                                     
                         importing the file into not a SAS dataset.                                                                                                                                 
                         The title statement contains the column headings                                                                                                                           
                                                                                                                                                                                                    
             b.   Proc corrresp ( hnadles the renaming and labeling(this is what tabulate and report should do)                                                                                     
                                                                                                                                                                                                    
                     Process                                                                                                                                                                        
                                                                                                                                                                                                    
                        Useg 'validvarname=any' to automatically get nice column headings'                                                                                                          
                        Run crresp to get counts                                                                                                                                                    
                        Run coreesp a second time to get sums. Requires weight statemet                                                                                                             
                        Merge counts and sums                                                                                                                                                       
                                                                                                                                                                                                    
                                                                                                                                                                                                    
             c.*  Output to excel and impot to sas tablerresp                                                                                                                                       
                  Exact duplicate of ops tabulate with a output dataset                                                                                                                             
                                                                                                                                                                                                    
                      Process                                                                                                                                                                       
                        Use ods excel and send tabulate to excel                                                                                                                                    
                        Import excel sheet to sas dataset                                                                                                                                           
                                                                                                                                                                                                    
             d.*     Wrap 'proc tabulate in my 'utl_odstab' macro                                                                                                                                   
                                                                                                                                                                                                    
                     Process                                                                                                                                                                        
                                                                                                                                                                                                    
                        Using the macro utl_odsrpt and 'proc printto' send the tabulate listng to a file                                                                                            
                        You need to construct a title statement with delimited column names.                                                                                                        
                        You need to tell the macro how many tabulate heading lines to skip.                                                                                                         
                        importing the file into not a SAS dataset.                                                                                                                                  
                                                                                                                                                                                                    
             e.   Using the proc report output dataset and renaming columns                                                                                                                         
                                                                                                                                                                                                    
                     Process                                                                                                                                                                        
                                                                                                                                                                                                    
                       Run report and specify an output dataset                                                                                                                                     
                       Use the listing output to figure ot the rename statement                                                                                                                     
                       Apply the rename statement.                                                                                                                                                  
                       There appears to be a bug with the ods output dataset.                                                                                                                       
                       It does not honor the nozero option on the define statement.                                                                                                                 
                                                                                                                                                                                                    
                                                                                                                                                                                                    
              * process c and d Have all the columns in the ops report in a SAS dataset                                                                                                             
                                                                                                                                                                                                    
    /*               _     _                                                                                                                                                                        
     _ __  _ __ ___ | |__ | | ___ _ __ ___                                                                                                                                                          
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \                                                                                                                                                         
    | |_) | | | (_) | |_) | |  __/ | | | | |                                                                                                                                                        
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|                                                                                                                                                        
    |_|                                                                                                                                                                                             
    */                                                                                                                                                                                              
                                                                                                                                                                                                    
    Problem                                                                                                                                                                                         
        Op wants this static tabulate output in a SAs table.                                                                                                                                        
                                                                                                                                                                                                    
    * this is the ops tabulate but formatted to fit on one page. Solution works with the mutipage output;                                                                                           
    options label ls=255;                                                                                                                                                                           
    Proc tabulate data =SASHelp.cars(where=( Origin in ('Asia' ,'Europe') and cylinders in (4,6,8)                                                                                                  
    and  Type in ('Hybrid','Suv','Sedan')))                                                                                                                                                         
     format=6.;                                                                                                                                                                                     
    label type="";                                                                                                                                                                                  
    Class Origin  Make  Cylinders   Type  ;                                                                                                                                                         
    Var Invoice;                                                                                                                                                                                    
    Table Origin=""*Make='',                                                                                                                                                                        
    Type=''*(Cylinders='' All="")*N=''                                                                                                                                                              
    all=''*N=''                                                                                                                                                                                     
                                                                                                                                                                                                    
    Type=''*(Cylinders='' All)*SUM=''*Invoice=''                                                                                                                                                    
    all=''*Invoice=''                                                                                                                                                                               
    /rts=28 box='make' misstext='0'  nocellmerge;                                                                                                                                                   
    format  Cylinders fCylinders.;                                                                                                                                                                  
    Run;                                                                                                                                                                                            
                                                                                                                                                                                                    
    -------------------------------------------------------------------------------------------------------------------                                                                             
    |make           |   Hybrid    |           Sedan           |      |   Hybrid    |           Sedan           |      |                                                                             
    |               |-------------+---------------------------|------|-------------+---------------------------|      |                                                                             
    |               |  4   | All  |  4   |  6   |  8   |  All |      |  4   | All  |  4   |  6   |  8   | All  | Sum  |                                                                             
    |---------------+------|------ ------+------+------|------+------ ------+------+------+------+------+------+------|                                                                             
    |Asia   |Acura  |     0|     0|     2|     3|     0|     5|     5|     0|     0| 46408|110413|     0|156821|156821|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Honda  |     1|     1|     7|     4|     0|    11|    12| 18451| 18451|111820| 92974|     0|204794|223245|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Hyundai|     0|     0|     6|     4|     0|    10|    10|     0|     0| 73627| 81495|     0|155122|155122|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Infini-|      |      |      |      |      |      |      |      |      |      |      |      |      |      |                                                                             
    |       |ti     |     0|     0|     0|     4|     2|     6|     6|     0|     0|     0|111796| 86367|198163|198163|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Kia    |     0|     0|     6|     3|     0|     9|     9|     0|     0| 73740| 60014|     0|133754|133754|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Lexus  |     0|     0|     0|     4|     2|     6|     6|     0|     0|     0|120966| 90815|211781|211781|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Mazda  |     0|     0|     3|     1|     0|     4|     4|     0|     0| 48264| 26600|     0| 74864| 74864|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Mitsub-|      |      |      |      |      |      |      |      |      |      |      |      |      |      |                                                                             
    |       |ishi   |     0|     0|     4|     2|     0|     6|     6|     0|     0| 63622| 51133|     0|114755|114755|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Nissan |     0|     0|     4|     5|     0|     9|     9|     0|     0| 60426|126705|     0|187131|187131|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Scion  |     0|     0|     1|     0|     0|     1|     1|     0|     0| 12340|     0|     0| 12340| 12340|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Subaru |     0|     0|     4|     2|     0|     6|     6|     0|     0| 85135| 55263|     0|140398|140398|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Suzuki |     0|     0|     4|     1|     0|     5|     5|     0|     0| 54530| 17053|     0| 71583| 71583|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Toyota |     1|     1|     8|     8|     0|    16|    17| 18926| 18926|107566|185029|     0|292595|311521|                                                                             
    |-------+-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |Europe |Audi   |     0|     0|     2|     8|     3|    13|    13|     0|     0| 56014|276961|153232|486207|486207|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |BMW    |     0|     0|     0|    10|     3|    13|    13|     0|     0|     0|335795|180290|516085|516085|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Jaguar |     0|     0|     0|     3|     5|     8|     8|     0|     0|     0| 98354|288863|387217|387217|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |MINI   |     0|     0|     2|     0|     0|     2|     2|     0|     0| 33574|     0|     0| 33574| 33574|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Merced-|      |      |      |      |      |      |      |      |      |      |      |      |      |      |                                                                             
    |       |es-Benz|     0|     0|     1|     9|     5|    15|    15|     0|     0| 24249|327694|340917|692860|692860|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Saab   |     0|     0|     6|     0|     0|     6|     6|     0|     0|210966|     0|     0|210966|210966|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Volksw-|      |      |      |      |      |      |      |      |      |      |      |      |      |      |                                                                             
    |       |agen   |     0|     0|     6|     2|     2|    10|    10|     0|     0|118450| 52269| 95964|266683|266683|                                                                             
    |       |-------+------+------+------+------+------+------+------+------+------+------+------+------+------+------|                                                                             
    |       |Volvo  |     0|     0|     1|     2|     0|     3|     3|     0|     0| 23701| 78115|     0|101816|101816|                                                                             
    -------------------------------------------------------------------------------------------------------------------                                                                             
                                                                                                                                                                                                    
                                                                                                                                                                                                    
    /*                   _                                                                                                                                                                          
    (_)_ __  _ __  _   _| |_                                                                                                                                                                        
    | | `_ \| `_ \| | | | __|                                                                                                                                                                       
    | | | | | |_) | |_| | |_                                                                                                                                                                        
    |_|_| |_| .__/ \__,_|\__|                                                                                                                                                                       
            |_|                                                                                                                                                                                     
    */                                                                                                                                                                                              
                                                                                                                                                                                                    
      SASHELP.CARS                                                                                                                                                                                  
                                                                                                                                                                                                    
    /*           _               _                                                                                                                                                                  
      ___  _   _| |_ _ __  _   _| |_ ___                                                                                                                                                            
     / _ \| | | | __| `_ \| | | | __/ __|                                                                                                                                                           
    | (_) | |_| | |_| |_) | |_| | |_\__ \                                                                                                                                                           
     \___/ \__,_|\__| .__/ \__,_|\__|___/                                                                                                                                                           
                    |_|                                                                                                                                                                             
      __ _     _ __ ___ _ __   ___  _ __| |_                                                                                                                                                        
     / _` |   | `__/ _ \ `_ \ / _ \| `__| __|                                                                                                                                                       
    | (_| |_  | | |  __/ |_) | (_) | |  | |_                                                                                                                                                        
     \__,_(_) |_|  \___| .__/ \___/|_|   \__|                                                                                                                                                       
                       |_|                                                                                                                                                                          
    */                                                                                                                                                                                              
                                                                                                                                                                                                    
    * this is all the code;                                                                                                                                                                         
                                                                                                                                                                                                    
    options validvarname=v7;                                                                                                                                                                        
    %utl_odsrpt(setup);                                                                                                                                                                             
    options ps=5000 ls=255 missing="0";                                                                                                                                                             
    proc report data=sashelp.cars(where=(                                                                                                                                                           
           cylinders in (4,6,8)              and                                                                                                                                                    
           Origin in ('Asia' ,'Europe')      and                                                                                                                                                    
           Type in ('Hybrid','Suv','Sedan')))                                                                                                                                                       
      nowd  out=want_rpt  formchar="|" noheader box;                                                                                                                                                
     format _numeric_ 14.;                                                                                                                                                                          
    cols  origin make (type, cylinders), (n invoice=_sum);                                                                                                                                          
    title "|make|origin|cnt_hybrid4|sum_hybrid4|cnt_sedan4|sum_invoice4|cnt_sedan6|sum_invoice6|cnt_sedan8|sum_invoice8|";                                                                          
    define origin / group;                                                                                                                                                                          
    define make / group;                                                                                                                                                                            
    define type / across nozero;                                                                                                                                                                    
    define cylinders / across nozero;                                                                                                                                                               
    run;quit;                                                                                                                                                                                       
    options ps=65;                                                                                                                                                                                  
    %utl_odsrpt(outdsn=want_odsrpt);                                                                                                                                                                
                                                                                                                                                                                                    
    * suggest you form the partial sums by hand. Less likely to get painted into a corner with the _C##_ variables;                                                                                 
                                                                                                                                                                                                    
    WANT_ODSRPT total obs=21                                                                                                                                                                        
                                                                                                                                                                                                    
                                 cnt_       sum_      cnt_       sum_       cnt_       sum_       cnt_       sum_                                                                                   
       make     origin           hybrid4    hybrid4    sedan4    invoice4    sedan6    invoice6    sedan8    invoice8                                                                               
                                                                                                                                                                                                    
      Asia      Acura               0            0        2        46408        3       110413        0            0                                                                                
                Honda               1        18451        7       111820        4        92974        0            0                                                                                
                Hyundai             0            0        6        73627        4        81495        0            0                                                                                
                Infiniti            0            0        0            0        4       111796        2        86367                                                                                
                Kia                 0            0        6        73740        3        60014        0            0                                                                                
                Lexus               0            0        0            0        4       120966        2        90815                                                                                
                Mazda               0            0        3        48264        1        26600        0            0                                                                                
                Mitsubishi          0            0        4        63622        2        51133        0            0                                                                                
                Nissan              0            0        4        60426        5       126705        0            0                                                                                
                Scion               0            0        1        12340        0            0        0            0                                                                                
                Subaru              0            0        4        85135        2        55263        0            0                                                                                
                Suzuki              0            0        4        54530        1        17053        0            0                                                                                
                Toyota              1        18926        8       107566        8       185029        0            0                                                                                
      Europe    Audi                0            0        2        56014        8       276961        3       153232                                                                                
                BMW                 0            0        0            0       10       335795        3       180290                                                                                
                Jaguar              0            0        0            0        3        98354        5       288863                                                                                
                MINI                0            0        2        33574        0            0        0            0                                                                                
                Mercedes-Benz       0            0        1        24249        9       327694        5       340917                                                                                
                Saab                0            0        6       210966        0            0        0            0                                                                                
                Volkswagen          0            0        6       118450        2        52269        2        95964                                                                                
                Volvo               0            0        1        23701        2        78115        0            0                                                                                
                                                                                                                                                                                                    
                                                                                                                                                                                                    
    options nolabel FORMCHAR='|----|+|---+=|-/\<>*';                                                                                                                                                
    proc report data=want_odsrpt nowd missing  box split='-' ;                                                                                                                                      
    format _numeric_ 7.;                                                                                                                                                                            
    run;quit;                                                                                                                                                                                       
                                                                                                                                                                                                    
    -----------------------------------------------------------------------------------------------                                                                                                 
    |                       cnt_hyb  sum_hyb  cnt_sed  sum_inv  cnt_sed  sum_inv  cnt_sed  sum_inv|                                                                                                 
    |make    origin            rid4     rid4      an4    oice4      an6    oice6      an8    oice8|                                                                                                 
    |---------------------------------------------------------------------------------------------|                                                                                                 
    |Asia  | Acura        |       0|       0|       2|   46408|       3|  110413|       0|       0|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Honda        |       1|   18451|       7|  111820|       4|   92974|       0|       0|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Hyundai      |       0|       0|       6|   73627|       4|   81495|       0|       0|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Infiniti     |       0|       0|       0|       0|       4|  111796|       2|   86367|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Kia          |       0|       0|       6|   73740|       3|   60014|       0|       0|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Lexus        |       0|       0|       0|       0|       4|  120966|       2|   90815|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Mazda        |       0|       0|       3|   48264|       1|   26600|       0|       0|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Mitsubishi   |       0|       0|       4|   63622|       2|   51133|       0|       0|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Nissan       |       0|       0|       4|   60426|       5|  126705|       0|       0|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Scion        |       0|       0|       1|   12340|       0|       0|       0|       0|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Subaru       |       0|       0|       4|   85135|       2|   55263|       0|       0|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Suzuki       |       0|       0|       4|   54530|       1|   17053|       0|       0|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Toyota       |       1|   18926|       8|  107566|       8|  185029|       0|       0|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |Europe| Audi         |       0|       0|       2|   56014|       8|  276961|       3|  153232|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | BMW          |       0|       0|       0|       0|      10|  335795|       3|  180290|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Jaguar       |       0|       0|       0|       0|       3|   98354|       5|  288863|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | MINI         |       0|       0|       2|   33574|       0|       0|       0|       0|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Mercedes-Benz|       0|       0|       1|   24249|       9|  327694|       5|  340917|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Saab         |       0|       0|       6|  210966|       0|       0|       0|       0|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Volkswagen   |       0|       0|       6|  118450|       2|   52269|       2|   95964|                                                                                                 
    |------+--------------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                                                 
    |      | Volvo        |       0|       0|       1|   23701|       2|   78115|       0|       0|                                                                                                 
    -----------------------------------------------------------------------------------------------                                                                                                 
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
    /*                                                                                                                                                                                              
    | |__      ___ ___  _ __ _ __ ___  ___ _ __                                                                                                                                                     
    | `_ \    / __/ _ \| `__| `__/ _ \/ __| `_ \                                                                                                                                                    
    | |_) |  | (_| (_) | |  | | |  __/\__ \ |_) |                                                                                                                                                   
    |_.__(_)  \___\___/|_|  |_|  \___||___/ .__/                                                                                                                                                    
                                          |_|                                                                                                                                                       
    */                                                                                                                                                                                              
    proc datasets lib=work;                                                                                                                                                                         
      delete havCnt havSum havtwo;                                                                                                                                                                  
    run;quit;                                                                                                                                                                                       
                                                                                                                                                                                                    
    ods exclude all;                                                                                                                                                                                
                                                                                                                                                                                                    
    options validvarname=v7;                                                                                                                                                                        
    ods output observed=havCnt(rename=sum=count_total);                                                                                                                                             
    proc corresp data=SASHelp.cars(where=(                                                                                                                                                          
           cylinders in (4,6,8)              and                                                                                                                                                    
           Origin in ('Asia' ,'Europe')      and                                                                                                                                                    
           Type in ('Hybrid','Suv','Sedan')))                                                                                                                                                       
      dim=1 observed cross=both;                                                                                                                                                                    
    tables origin make, type cylinders ;                                                                                                                                                            
    run;quit;                                                                                                                                                                                       
                                                                                                                                                                                                    
                                                                                                                                                                                                    
    ods output observed=havSum (drop=sum rename=(                                                                                                                                                   
           Hybrid___4 =  sum_Hybrid___4                                                                                                                                                             
           Sedan___4  =  sum_Sedan___4                                                                                                                                                              
           Sedan___6  =  sum_Sedan___6                                                                                                                                                              
           Sedan___8  =  sum_Sedan___8                                                                                                                                                              
           ));                                                                                                                                                                                      
                                                                                                                                                                                                    
    proc corresp data=SASHelp.cars(where=(                                                                                                                                                          
           cylinders in (4,6,8)              and                                                                                                                                                    
           Origin in ('Asia' ,'Europe')      and                                                                                                                                                    
           Type in ('Hybrid','Suv','Sedan')))                                                                                                                                                       
      dim=1 observed cross=both;                                                                                                                                                                    
    tables origin make, type cylinders;                                                                                                                                                             
    weight invoice;                                                                                                                                                                                 
    run;quit;                                                                                                                                                                                       
    ods select all;                                                                                                                                                                                 
                                                                                                                                                                                                    
    data havtwo;                                                                                                                                                                                    
      merge                                                                                                                                                                                         
         havCnt                                                                                                                                                                                     
         havSum                                                                                                                                                                                     
      ;                                                                                                                                                                                             
      sum__sedan___all =sum(of sum_Sedan___:);                                                                                                                                                      
      label sum__sedan___all="Sedan * Sum";                                                                                                                                                         
    run;quit;                                                                                                                                                                                       
                                                                                                                                                                                                    
    options label;                                                                                                                                                                                  
                                                                                                                                                                                                    
    proc report data=havtwo nowd missing box;                                                                                                                                                       
    format _numeric_ 10.;                                                                                                                                                                           
    run;quit;                                                                                                                                                                                       
                                                                                                                                                                                                    
                                                                                                                                                                                                    
    Note I suppressed Hybrid sums because there is only 4 cylinder Hybrids.                                                                                                                         
    Since you have a dataset you can add or subtract clolumns.                                                                                                                                      
                                                                                                                                                                                                    
    WORK.HAVTWO total obs=22                                                                                                                                                                        
                                                                                                                                                                                                    
                                                                                                             sum_        sum_        sum_        sum__                                              
                               Hybrid___    Sedan___    Sedan___    Sedan___    count_    sum_Hybrid___    Sedan___    Sedan___    Sedan___    sedan___                                             
     Label                         4            4           6           8        total          4              4           6           8          all                                               
                                                                                                                                                                                                    
     Asia * Acura                  0            2           3           0           5             0           46408      110413           0         5                                               
     Asia * Honda                  1            7           4           0          12         18451          111820       92974           0        11                                               
     Asia * Hyundai                0            6           4           0          10             0           73627       81495           0        10                                               
     Asia * Infiniti               0            0           4           2           6             0               0      111796       86367         6                                               
     Asia * Kia                    0            6           3           0           9             0           73740       60014           0         9                                               
     Asia * Lexus                  0            0           4           2           6             0               0      120966       90815         6                                               
     Asia * Mazda                  0            3           1           0           4             0           48264       26600           0         4                                               
     Asia * Mitsubishi             0            4           2           0           6             0           63622       51133           0         6                                               
     Asia * Nissan                 0            4           5           0           9             0           60426      126705           0         9                                               
     Asia * Scion                  0            1           0           0           1             0           12340           0           0         1                                               
     Asia * Subaru                 0            4           2           0           6             0           85135       55263           0         6                                               
     Asia * Suzuki                 0            4           1           0           5             0           54530       17053           0         5                                               
     Asia * Toyota                 1            8           8           0          17         18926          107566      185029           0        16                                               
     Europe * Audi                 0            2           8           3          13             0           56014      276961      153232        13                                               
     Europe * BMW                  0            0          10           3          13             0               0      335795      180290        13                                               
     Europe * Jaguar               0            0           3           5           8             0               0       98354      288863         8                                               
     Europe * MINI                 0            2           0           0           2             0           33574           0           0         2                                               
     Europe * Mercedes-Benz        0            1           9           5          15             0           24249      327694      340917        15                                               
     Europe * Saab                 0            6           0           0           6             0          210966           0           0         6                                               
     Europe * Volkswagen           0            6           2           2          10             0          118450       52269       95964        10                                               
     Europe * Volvo                0            1           2           0           3             0           23701       78115           0         3                                               
                                                                                                                                                                                                    
     Sum                           2           67          75          22         166         37377         1204432     2208629     1236448       164                                               
                                                                                                                                                                                                    
    The report code is trivial after you have the dataset                                                                                                                                           
                                                                                                                                                                                                    
    * this report is using the labels;                                                                                                                                                              
    options label;                                                                                                                                                                                  
    proc report data=havtwo nowd missing  box split='*';                                                                                                                                            
    format _numeric_ 7.;                                                                                                                                                                            
    run;quit;                                                                                                                                                                                       
                                                                                                                                                                                                    
    ------------------------------------------------------------------------------------------------------------------                                                                              
    |                         Hybrid    Sedan    Sedan    Sedan  count_t   Hybrid    Sedan    Sedan    Sedan    Sedan|                                                                              
    |Label                         4        4        6        8     otal        4        4        6        8      Sum|                                                                              
    |----------------------------------------------------------------------------------------------------------------|                                                                              
    |Asia * Acura          |       0|       2|       3|       0|       5|       0|   46408|  110413|       0|  156821|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Asia * Honda          |       1|       7|       4|       0|      12|   18451|  111820|   92974|       0|  204794|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Asia * Hyundai        |       0|       6|       4|       0|      10|       0|   73627|   81495|       0|  155122|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Asia * Infiniti       |       0|       0|       4|       2|       6|       0|       0|  111796|   86367|  198163|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Asia * Kia            |       0|       6|       3|       0|       9|       0|   73740|   60014|       0|  133754|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Asia * Lexus          |       0|       0|       4|       2|       6|       0|       0|  120966|   90815|  211781|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Asia * Mazda          |       0|       3|       1|       0|       4|       0|   48264|   26600|       0|   74864|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Asia * Mitsubishi     |       0|       4|       2|       0|       6|       0|   63622|   51133|       0|  114755|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Asia * Nissan         |       0|       4|       5|       0|       9|       0|   60426|  126705|       0|  187131|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Asia * Scion          |       0|       1|       0|       0|       1|       0|   12340|       0|       0|   12340|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Asia * Subaru         |       0|       4|       2|       0|       6|       0|   85135|   55263|       0|  140398|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Asia * Suzuki         |       0|       4|       1|       0|       5|       0|   54530|   17053|       0|   71583|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Asia * Toyota         |       1|       8|       8|       0|      17|   18926|  107566|  185029|       0|  292595|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Europe * Audi         |       0|       2|       8|       3|      13|       0|   56014|  276961|  153232|  486207|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Europe * BMW          |       0|       0|      10|       3|      13|       0|       0|  335795|  180290|  516085|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Europe * Jaguar       |       0|       0|       3|       5|       8|       0|       0|   98354|  288863|  387217|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Europe * MINI         |       0|       2|       0|       0|       2|       0|   33574|       0|       0|   33574|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Europe * Mercedes-Benz|       0|       1|       9|       5|      15|       0|   24249|  327694|  340917|  692860|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Europe * Saab         |       0|       6|       0|       0|       6|       0|  210966|       0|       0|  210966|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Europe * Volkswagen   |       0|       6|       2|       2|      10|       0|  118450|   52269|   95964|  266683|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Europe * Volvo        |       0|       1|       2|       0|       3|       0|   23701|   78115|       0|  101816|                                                                              
    |----------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------|                                                                              
    |Sum                   |       2|      67|      75|      22|     166|   37377| 1204432| 2208629| 1236448| 4649509|                                                                              
    ------------------------------------------------------------------------------------------------------------------                                                                              
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
    /*                          _                                                                                                                                                                   
      ___      _____  _____ ___| |                                                                                                                                                                  
     / __|    / _ \ \/ / __/ _ \ |                                                                                                                                                                  
    | (__ _  |  __/>  < (_|  __/ |                                                                                                                                                                  
     \___(_)  \___/_/\_\___\___|_|                                                                                                                                                                  
                                                                                                                                                                                                    
    */                                                                                                                                                                                              
                                                                                                                                                                                                    
    %utlfkil(d:/xls/xfrx.xlsx);                                                                                                                                                                     
                                                                                                                                                                                                    
    proc datasets lib=work nolist;                                                                                                                                                                  
      delete want_excel;                                                                                                                                                                            
    run;quit;                                                                                                                                                                                       
                                                                                                                                                                                                    
    options validvarname=any;                                                                                                                                                                       
    options nolabel FORMCHAR='|----|+|---+=|-/\<>*';                                                                                                                                                
    ods excel file="d:/xls/xfrx.xlsx" style=minimal options(sheet_name="xfr");                                                                                                                      
                                                                                                                                                                                                    
    Proc tabulate data =SASHelp.cars(where=(                                                                                                                                                        
           cylinders in (4,6,8)              and                                                                                                                                                    
           Origin in ('Asia' ,'Europe')      and                                                                                                                                                    
           Type in ('Hybrid','Suv','Sedan')))                                                                                                                                                       
     format=comma6.;                                                                                                                                                                                
    Class Origin  Make  Cylinders   Type  ;                                                                                                                                                         
    Var Invoice;                                                                                                                                                                                    
    Table Origin=""*Make='',                                                                                                                                                                        
    Type='Nr Customers'*(Cylinders='' All)*N=''                                                                                                                                                     
    all=''*N='TOTAL'                                                                                                                                                                                
                                                                                                                                                                                                    
    Type='Total Revenue'*(Cylinders='' All)*SUM=''*Invoice=''                                                                                                                                       
    all=''*Invoice='TOTAL'                                                                                                                                                                          
    /box='Make/Origin' misstext='0'  nocellmerge rts=28;                                                                                                                                            
    format  Cylinders fCylinders.;                                                                                                                                                                  
    Run;                                                                                                                                                                                            
    ods excel close;                                                                                                                                                                                
                                                                                                                                                                                                    
    libname xel "d:/xls/xfrx.xlsx" header=no;                                                                                                                                                       
                                                                                                                                                                                                    
                                                                                                                                                                                                    
    options validvarname=any;                                                                                                                                                                       
    data want_excel;                                                                                                                                                                                
      set xel.'xfr$A4:Z99'n;                                                                                                                                                                        
      rename                                                                                                                                                                                        
          F1 = make                                                                                                                                                                                 
          F2 = origin                                                                                                                                                                               
          F3 = cnt_hybrid_4                                                                                                                                                                         
          F4 = cnt_hybrid_all                                                                                                                                                                       
          F5 = cnt_sedan_4                                                                                                                                                                          
          F6 = cnt_sedan_6                                                                                                                                                                          
          F7 = cnt_sedan_8                                                                                                                                                                          
          F8 = cnt_sedan_all                                                                                                                                                                        
          F9 = cnt_hybrid_sedan_all                                                                                                                                                                 
          F10= sum_hybrid_4                                                                                                                                                                         
          F11= sum_hybrid_all                                                                                                                                                                       
          F12= sum_sedan_4                                                                                                                                                                          
          F13= sum_sedan_6                                                                                                                                                                          
          F14= sum_sedan_8                                                                                                                                                                          
          F15= sum_sedan_all                                                                                                                                                                        
          F16= sum_all                                                                                                                                                                              
           ;                                                                                                                                                                                        
    run;quit;                                                                                                                                                                                       
    libname xel clear;                                                                                                                                                                              
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
    WORK.WANT_EXCEL total obs=21                                                                                                                                                                    
                               cnt_    cnt_hybrid_    cnt_     cnt_     cnt_   sedan_  cnt_hybrid_    sum_    sum_hybrid_    sum_     sum_     sum_   sedan_                                        
     cnt_make  cnt_origin     hybrid_4      all      sedan_4  sedan_6  sedan_8    all    sedan_all   hybrid_4      all      sedan_4  sedan_6  sedan_8    all   sum_all                              
                                                                                                                                                                                                    
      Asia     Acura              0          0          2         3       0        5          5            0          0       46408   110413        0  156821   156821                              
               Honda              1          1          7         4       0       11         12        18451      18451      111820    92974        0  204794   223245                              
               Hyundai            0          0          6         4       0       10         10            0          0       73627    81495        0  155122   155122                              
               Infiniti           0          0          0         4       2        6          6            0          0           0   111796    86367  198163   198163                              
               Kia                0          0          6         3       0        9          9            0          0       73740    60014        0  133754   133754                              
               Lexus              0          0          0         4       2        6          6            0          0           0   120966    90815  211781   211781                              
               Mazda              0          0          3         1       0        4          4            0          0       48264    26600        0   74864    74864                              
               Mitsubishi         0          0          4         2       0        6          6            0          0       63622    51133        0  114755   114755                              
               Nissan             0          0          4         5       0        9          9            0          0       60426   126705        0  187131   187131                              
               Scion              0          0          1         0       0        1          1            0          0       12340        0        0   12340    12340                              
               Subaru             0          0          4         2       0        6          6            0          0       85135    55263        0  140398   140398                              
               Suzuki             0          0          4         1       0        5          5            0          0       54530    17053        0   71583    71583                              
               Toyota             1          1          8         8       0       16         17        18926      18926      107566   185029        0  292595   311521                              
      Europe   Audi               0          0          2         8       3       13         13            0          0       56014   276961   153232  486207   486207                              
               BMW                0          0          0        10       3       13         13            0          0           0   335795   180290  516085   516085                              
               Jaguar             0          0          0         3       5        8          8            0          0           0    98354   288863  387217   387217                              
               MINI               0          0          2         0       0        2          2            0          0       33574        0        0   33574    33574                              
               Mercedes-Benz      0          0          1         9       5       15         15            0          0       24249   327694   340917  692860   692860                              
               Saab               0          0          6         0       0        6          6            0          0      210966        0        0  210966   210966                              
               Volkswagen         0          0          6         2       2       10         10            0          0      118450    52269    95964  266683   266683                              
               Volvo              0          0          1         2       0        3          3            0          0       23701    78115        0  101816   101816                              
                                                                                                                                                                                                    
                                                                                                                                                                                                    
    options nolabel FORMCHAR='|----|+|---+=|-/\<>*';                                                                                                                                                
    option nolabel;                                                                                                                                                                                 
    proc report data=want_excel nowd missing split='_' box;                                                                                                                                         
    format _numeric_ 8.;                                                                                                                                                                            
    run;quit;                                                                                                                                                                                       
                                                                                                                                                                                                    
    -------------------------------------------------------------------------------------------------------------------------------------------------------------------                             
    |                                                                                        cnt                                                                      |                             
    |                            cnt       cnt       cnt       cnt       cnt       cnt    hybrid       sum       sum       sum       sum       sum       sum          |                             
    |                         hybrid    hybrid     sedan     sedan     sedan     sedan     sedan    hybrid    hybrid     sedan     sedan     sedan     sedan       sum|                             
    |make    origin                4       all         4         6         8       all       all         4       all         4         6         8       all       all|                             
    |-----------------------------------------------------------------------------------------------------------------------------------------------------------------|                             
    |Asia  | Acura        |        0|        0|        2|        3|        0|        5|        5|        0|        0|    46408|   110413|        0|   156821|   156821|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Honda        |        1|        1|        7|        4|        0|       11|       12|    18451|    18451|   111820|    92974|        0|   204794|   223245|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Hyundai      |        0|        0|        6|        4|        0|       10|       10|        0|        0|    73627|    81495|        0|   155122|   155122|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Infiniti     |        0|        0|        0|        4|        2|        6|        6|        0|        0|        0|   111796|    86367|   198163|   198163|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Kia          |        0|        0|        6|        3|        0|        9|        9|        0|        0|    73740|    60014|        0|   133754|   133754|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Lexus        |        0|        0|        0|        4|        2|        6|        6|        0|        0|        0|   120966|    90815|   211781|   211781|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Mazda        |        0|        0|        3|        1|        0|        4|        4|        0|        0|    48264|    26600|        0|    74864|    74864|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Mitsubishi   |        0|        0|        4|        2|        0|        6|        6|        0|        0|    63622|    51133|        0|   114755|   114755|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Nissan       |        0|        0|        4|        5|        0|        9|        9|        0|        0|    60426|   126705|        0|   187131|   187131|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Scion        |        0|        0|        1|        0|        0|        1|        1|        0|        0|    12340|        0|        0|    12340|    12340|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Subaru       |        0|        0|        4|        2|        0|        6|        6|        0|        0|    85135|    55263|        0|   140398|   140398|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Suzuki       |        0|        0|        4|        1|        0|        5|        5|        0|        0|    54530|    17053|        0|    71583|    71583|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Toyota       |        1|        1|        8|        8|        0|       16|       17|    18926|    18926|   107566|   185029|        0|   292595|   311521|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |Europe| Audi         |        0|        0|        2|        8|        3|       13|       13|        0|        0|    56014|   276961|   153232|   486207|   486207|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | BMW          |        0|        0|        0|       10|        3|       13|       13|        0|        0|        0|   335795|   180290|   516085|   516085|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Jaguar       |        0|        0|        0|        3|        5|        8|        8|        0|        0|        0|    98354|   288863|   387217|   387217|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | MINI         |        0|        0|        2|        0|        0|        2|        2|        0|        0|    33574|        0|        0|    33574|    33574|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Mercedes-Benz|        0|        0|        1|        9|        5|       15|       15|        0|        0|    24249|   327694|   340917|   692860|   692860|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Saab         |        0|        0|        6|        0|        0|        6|        6|        0|        0|   210966|        0|        0|   210966|   210966|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Volkswagen   |        0|        0|        6|        2|        2|       10|       10|        0|        0|   118450|    52269|    95964|   266683|   266683|                             
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                             
    |      | Volvo        |        0|        0|        1|        2|        0|        3|        3|        0|        0|    23701|    78115|        0|   101816|   101816|                             
    -------------------------------------------------------------------------------------------------------------------------------------------------------------------                             
                                                                                                                                                                                                    
    /*   _                                                _       _        _                                                                                                                        
      __| |    _ __ ___   __ _  ___ _ __ ___     ___   __| |___  | |_ __ _| |__                                                                                                                     
     / _` |   | `_ ` _ \ / _` |/ __| `__/ _ \   / _ \ / _` / __| | __/ _` | `_ \                                                                                                                    
    | (_| |_  | | | | | | (_| | (__| | | (_) | | (_) | (_| \__ \ | || (_| | |_) |                                                                                                                   
     \__,_(_) |_| |_| |_|\__,_|\___|_|  \___/   \___/ \__,_|___/  \__\__,_|_.__/                                                                                                                    
                                                                                                                                                                                                    
    */                                                                                                                                                                                              
                                                                                                                                                                                                    
                                                                                                                                                                                                    
    proc datasets lib=work nolist;                                                                                                                                                                  
     delete want_tab;                                                                                                                                                                               
    run;quit;                                                                                                                                                                                       
                                                                                                                                                                                                    
    %utl_odstab(setup);                                                                                                                                                                             
    options label ls=255;                                                                                                                                                                           
    Proc tabulate data =SASHelp.cars(where=( Origin in ('Asia' ,'Europe') and cylinders in (4,6,8)                                                                                                  
         and  Type in ('Hybrid','Suv','Sedan'))) format=6.;                                                                                                                                         
    title "|make|origin|cnt_hyb4|cnt_hyball|cnt_sdn4|cnt_sdn6|cnt_sdn8|cnt_sdnall|cnt_hybsdnall|sum_hyb4|sum_hyball|sum_sdn4|sum_sdn6|sum_sdn8|sum_sdnall|sum_all|";                                
    Class Origin  Make  Cylinders   Type  ;                                                                                                                                                         
    Var Invoice;                                                                                                                                                                                    
    Table Origin=""*Make='',                                                                                                                                                                        
    Type=''*(Cylinders='' All="")*N=''                                                                                                                                                              
    all=''*N=''                                                                                                                                                                                     
    Type=''*(Cylinders='' All)*SUM=''*Invoice=''                                                                                                                                                    
    all=''*Invoice=''                                                                                                                                                                               
    /rts=17 box='make' misstext='0'  nocellmerge;                                                                                                                                                   
    format  Cylinders fCylinders.;                                                                                                                                                                  
    Run;                                                                                                                                                                                            
    %utl_odstab(outdsn=want_tab,datarow=3);                                                                                                                                                         
                                                                                                                                                                                                    
                                                                                                                                                                                                    
    WORK.WANT_TAB total obs=25                                                                                                                                                                      
                                                                                                                                                                                                    
                                      CNT_                                          CNT_        CNT_                   SUM_                                          SUM_                           
      MAKE     ORIGIN     CNT_HYB4    HYBALL    CNT_SDN4    CNT_SDN6    CNT_SDN8    SDNALL    HYBSDNALL    SUM_HYB4    HYBALL    SUM_SDN4    SUM_SDN6    SUM_SDN8    SDNALL    SUM_ALL              
                                                                                                                                                                                                    
     Asia      Acura          0          0          2           3           0          5           5             0          0      46408      110413           0     156821     156821              
               Honda          1          1          7           4           0         11          12         18451      18451     111820       92974           0     204794     223245              
               Hyundai        0          0          6           4           0         10          10             0          0      73627       81495           0     155122     155122              
               Infini-        .          .          .           .           .          .           .             .          .          .           .           .          .          .              
               ti             0          0          0           4           2          6           6             0          0          0      111796       86367     198163     198163              
               Kia            0          0          6           3           0          9           9             0          0      73740       60014           0     133754     133754              
               Lexus          0          0          0           4           2          6           6             0          0          0      120966       90815     211781     211781              
               Mazda          0          0          3           1           0          4           4             0          0      48264       26600           0      74864      74864              
               Mitsub-        .          .          .           .           .          .           .             .          .          .           .           .          .          .              
               ishi           0          0          4           2           0          6           6             0          0      63622       51133           0     114755     114755              
               Nissan         0          0          4           5           0          9           9             0          0      60426      126705           0     187131     187131              
               Scion          0          0          1           0           0          1           1             0          0      12340           0           0      12340      12340              
               Subaru         0          0          4           2           0          6           6             0          0      85135       55263           0     140398     140398              
               Suzuki         0          0          4           1           0          5           5             0          0      54530       17053           0      71583      71583              
               Toyota         1          1          8           8           0         16          17         18926      18926     107566      185029           0     292595     311521              
     Europe    Audi           0          0          2           8           3         13          13             0          0      56014      276961      153232     486207     486207              
               BMW            0          0          0          10           3         13          13             0          0          0      335795      180290     516085     516085              
               Jaguar         0          0          0           3           5          8           8             0          0          0       98354      288863     387217     387217              
               MINI           0          0          2           0           0          2           2             0          0      33574           0           0      33574      33574              
               Merced-        .          .          .           .           .          .           .             .          .          .           .           .          .          .              
               es-Benz        0          0          1           9           5         15          15             0          0      24249      327694      340917     692860     692860              
               Saab           0          0          6           0           0          6           6             0          0     210966           0           0     210966     210966              
               Volksw-        .          .          .           .           .          .           .             .          .          .           .           .          .          .              
               agen           0          0          6           2           2         10          10             0          0     118450       52269       95964     266683     266683              
               Volvo          0          0          1           2           0          3           3             0          0      23701       78115           0     101816     101816              
                                                                                                                                                                                                    
    /*                                  _                 _                                                                                                                                         
      ___     _ __ ___ _ __   ___  _ __| |_    ___  _   _| |_                                                                                                                                       
     / _ \   | `__/ _ \ `_ \ / _ \| `__| __|  / _ \| | | | __|                                                                                                                                      
    |  __/_  | | |  __/ |_) | (_) | |  | |_  | (_) | |_| | |_                                                                                                                                       
     \___(_) |_|  \___| .__/ \___/|_|   \__|  \___/ \__,_|\__|                                                                                                                                      
                      |_|                                                                                                                                                                           
         _       _                 _                                                                                                                                                                
      __| | __ _| |_ __ _ ___  ___| |_                                                                                                                                                              
     / _` |/ _` | __/ _` / __|/ _ \ __|                                                                                                                                                             
    | (_| | (_| | || (_| \__ \  __/ |_                                                                                                                                                              
     \__,_|\__,_|\__\__,_|___/\___|\__|                                                                                                                                                             
                                                                                                                                                                                                    
    */                                                                                                                                                                                              
                                                                                                                                                                                                    
    options nolabel FORMCHAR='|----|+|---+=|-/\<>*';                                                                                                                                                
    ods output report=want_zro                                                                                                                                                                      
        (rename=(                                                                                                                                                                                   
           _c3_  = cnt_hybrid4                                                                                                                                                                      
           _c4_  = sum_hybrid4                                                                                                                                                                      
                                                                                                                                                                                                    
           _c5_  = cnt_hybrid6 /* you may want to drop these because option nozero fails */                                                                                                         
           _c6_  = sum_hybrid6                                                                                                                                                                      
           _c7_  = cnt_hybrid8                                                                                                                                                                      
           _c8_  = sum_hybrid8                                                                                                                                                                      
                                                                                                                                                                                                    
           _c9_  = cnt_sedan4                                                                                                                                                                       
           _c10_ = sum_invoice4                                                                                                                                                                     
           _c11_ = cnt_sedan6                                                                                                                                                                       
           _c12_ = sum_invoice6                                                                                                                                                                     
           _c13_ = cnt_sedan8                                                                                                                                                                       
           _c14_ = sum_invoice8                                                                                                                                                                     
           ));                                                                                                                                                                                      
    proc report data=sashelp.cars(where=(                                                                                                                                                           
           cylinders in (4,6,8)              and                                                                                                                                                    
           Origin in ('Asia' ,'Europe')      and                                                                                                                                                    
           Type in ('Hybrid','Suv','Sedan')))                                                                                                                                                       
                                                                                                                                                                                                    
           nowd                                                                                                                                                                                     
           FORMCHAR='|----|+|---+=|-/\<>*'                                                                                                                                                          
           box                                                                                                                                                                                      
           ;                                                                                                                                                                                        
     format _numeric_ 7.;                                                                                                                                                                           
    cols  origin make (type, cylinders), (n invoice=_sum);                                                                                                                                          
    title "|make|origin|cnt_hybrid4|sum_hybrid4|cnt_sedan4|sum_invoice4|cnt_sedan6|sum_invoice6|cnt_sedan8|sum_invoice8|";                                                                          
    define origin / group;                                                                                                                                                                          
    define make / group;                                                                                                                                                                            
    define type / across      /* NOZERO is not supported in ods output (we get all crossings) */;                                                                                                   
    define cylinders / across /* NOZERO is not supported in ods output */;                                                                                                                          
    run;quit;                                                                                                                                                                                       
                                                                                                                                                                                                    
    /* listing and dataset headers. Useful for renaming.                                                                                                                                            
                                                                                                                                                                                                    
    -----------------------------------------------------------------------------------------------------------------------------------------------                                                 
    |                                                                                TYPE                                                         |                                                 
    |                                                 Hybrid                                                      Sedan                           |                                                 
    |                                               CYLINDERS                                                   CYLINDERS                         |                                                 
    |                               4                   6                   8                   4                   6                   8         |                                                 
    |ORIGIN  MAKE                   n  INVOICE          n  INVOICE          n  INVOICE          n  INVOICE          n  INVOICE          n  INVOICE|                                                 
    |---------------------------------------------------------------------------------------------------------------------------------------------|                                                 
    |Asia  | Acura        |         .|       .|         .|       .|         .|       .|         2|   46408|         3|  110413|         .|       .|                                                 
    |      |--------------+----------+--------+----------+--------+----------+--------+----------+--------+----------+--------+----------+--------|                                                 
    |      |              |          |        |          |        |          |        |          |        |          |        |          |        |                                                 
     ORIGIN|---MAKE-------+-----_C3_-+---_C4_-+----_C5_--+-_C6_---+----_C7_--+-_C8_---+---_C9_---+-_C10_--+---_C11_--+--_C12_-+--_C13_---+-_C14_--|                                                 
    |      |              |          |        |          |        |          |        |          |        |          |        |          |        |                                                 
           |--------------+----------+--------+----------+--------+----------+--------+----------+--------+----------+--------+----------+--------|                                                 
    */                                                                                                                                                                                              
                                                                                                                                                                                                    
    title;                                                                                                                                                                                          
    options nolabel FORMCHAR='|----|+|---+=|-/\<>*';                                                                                                                                                
    option nolabel;                                                                                                                                                                                 
    proc report data=want_zro(Drop=_break_) nowd missing split='_' box;                                                                                                                             
    format _numeric_ 8.;                                                                                                                                                                            
    run;quit;                                                                                                                                                                                       
                                                                                                                                                                                                    
    -----------------------------------------------------------------------------------------------------------------------------------------------                                                 
    |                            CNT       SUM       CNT       SUM       CNT       SUM       CNT       SUM       CNT       SUM       CNT       SUM|                                                 
    |ORIGIN  MAKE            HYBRID4   HYBRID4   HYBRID6   HYBRID6   HYBRID8   HYBRID8    SEDAN4  INVOICE4    SEDAN6  INVOICE6    SEDAN8  INVOICE8|                                                 
    |---------------------------------------------------------------------------------------------------------------------------------------------|                                                 
    |Asia  | Acura        |        .|        .|        .|        .|        .|        .|        2|    46408|        3|   110413|        .|        .|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Asia  | Honda        |        1|    18451|        .|        .|        .|        .|        7|   111820|        4|    92974|        .|        .|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Asia  | Hyundai      |        .|        .|        .|        .|        .|        .|        6|    73627|        4|    81495|        .|        .|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Asia  | Infiniti     |        .|        .|        .|        .|        .|        .|        .|        .|        4|   111796|        2|    86367|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Asia  | Kia          |        .|        .|        .|        .|        .|        .|        6|    73740|        3|    60014|        .|        .|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Asia  | Lexus        |        .|        .|        .|        .|        .|        .|        .|        .|        4|   120966|        2|    90815|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Asia  | Mazda        |        .|        .|        .|        .|        .|        .|        3|    48264|        1|    26600|        .|        .|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Asia  | Mitsubishi   |        .|        .|        .|        .|        .|        .|        4|    63622|        2|    51133|        .|        .|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Asia  | Nissan       |        .|        .|        .|        .|        .|        .|        4|    60426|        5|   126705|        .|        .|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Asia  | Scion        |        .|        .|        .|        .|        .|        .|        1|    12340|        .|        .|        .|        .|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Asia  | Subaru       |        .|        .|        .|        .|        .|        .|        4|    85135|        2|    55263|        .|        .|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Asia  | Suzuki       |        .|        .|        .|        .|        .|        .|        4|    54530|        1|    17053|        .|        .|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Asia  | Toyota       |        1|    18926|        .|        .|        .|        .|        8|   107566|        8|   185029|        .|        .|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Europe| Audi         |        .|        .|        .|        .|        .|        .|        2|    56014|        8|   276961|        3|   153232|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Europe| BMW          |        .|        .|        .|        .|        .|        .|        .|        .|       10|   335795|        3|   180290|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Europe| Jaguar       |        .|        .|        .|        .|        .|        .|        .|        .|        3|    98354|        5|   288863|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Europe| MINI         |        .|        .|        .|        .|        .|        .|        2|    33574|        .|        .|        .|        .|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Europe| Mercedes-Benz|        .|        .|        .|        .|        .|        .|        1|    24249|        9|   327694|        5|   340917|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Europe| Saab         |        .|        .|        .|        .|        .|        .|        6|   210966|        .|        .|        .|        .|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Europe| Volkswagen   |        .|        .|        .|        .|        .|        .|        6|   118450|        2|    52269|        2|    95964|                                                 
    |------+--------------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------+---------|                                                 
    |Europe| Volvo        |        .|        .|        .|        .|        .|        .|        1|    23701|        2|    78115|        .|        .|                                                 
    -----------------------------------------------------------------------------------------------------------------------------------------------                                                 
                                                                                                                                                                                                    
    /*                                                                                                                                                                                              
     _ __ ___   __ _  ___ _ __ ___                                                                                                                                                                  
    | `_ ` _ \ / _` |/ __| `__/ _ \                                                                                                                                                                 
    | | | | | | (_| | (__| | | (_) |                                                                                                                                                                
    |_| |_| |_|\__,_|\___|_|  \___/                                                                                                                                                                 
                                                                                                                                                                                                    
           _   _              _     _        _                                                                                                                                                      
     _   _| |_| |    ___   __| |___| |_ __ _| |__                                                                                                                                                   
    | | | | __| |   / _ \ / _` / __| __/ _` | `_ \                                                                                                                                                  
    | |_| | |_| |  | (_) | (_| \__ \ || (_| | |_) |                                                                                                                                                 
     \__,_|\__|_|___\___/ \__,_|___/\__\__,_|_.__/                                                                                                                                                  
               |_____|                                                                                                                                                                              
    */                                                                                                                                                                                              
    %macro utl_odstab(outdsn,datarow=1);                                                                                                                                                            
                                                                                                                                                                                                    
       %if %qupcase(&outdsn)=SETUP %then %do;                                                                                                                                                       
                                                                                                                                                                                                    
            filename _tmp1_ clear;  * just in case;                                                                                                                                                 
                                                                                                                                                                                                    
            %utlfkil(%sysfunc(pathname(work))/_tmp1_.txt);                                                                                                                                          
                                                                                                                                                                                                    
            filename _tmp1_ "%sysfunc(pathname(work))/_tmp1_.txt";                                                                                                                                  
                                                                                                                                                                                                    
            %let _ps_= %sysfunc(getoption(ps));                                                                                                                                                     
            %let _fc_= %sysfunc(getoption(formchar));                                                                                                                                               
                                                                                                                                                                                                    
            OPTIONS ls=max ps=32756  FORMCHAR='|'  nodate nocenter;                                                                                                                                 
                                                                                                                                                                                                    
            title; footnote;                                                                                                                                                                        
                                                                                                                                                                                                    
            proc printto print=_tmp1_;                                                                                                                                                              
            run;quit;                                                                                                                                                                               
                                                                                                                                                                                                    
       %end;                                                                                                                                                                                        
       %else %do;                                                                                                                                                                                   
                                                                                                                                                                                                    
            /* %let outdsn=tst; %let datarow=3; */                                                                                                                                                  
                                                                                                                                                                                                    
            proc printto;                                                                                                                                                                           
            run;quit;                                                                                                                                                                               
                                                                                                                                                                                                    
            %utlfkil(%sysfunc(pathname(work))/_tmp2_.txt);                                                                                                                                          
                                                                                                                                                                                                    
            *filename _tmp2_  "%sysfunc(pathname(work))/_tmp2_.txt";                                                                                                                                
                                                                                                                                                                                                    
            proc datasets lib=work nolist;  *just in case;                                                                                                                                          
             delete &outdsn;                                                                                                                                                                        
            run;quit;                                                                                                                                                                               
                                                                                                                                                                                                    
            proc printto print="%sysfunc(pathname(work))/_tmp2_.txt";                                                                                                                               
            run;quit;                                                                                                                                                                               
                                                                                                                                                                                                    
            data _null_;                                                                                                                                                                            
              retain n 0;                                                                                                                                                                           
              infile _tmp1_ length=l;                                                                                                                                                               
              input lyn $varying32756. l;                                                                                                                                                           
              if _n_=1 then do;                                                                                                                                                                     
                  file print titles;                                                                                                                                                                
                  putlog lyn;                                                                                                                                                                       
                  *put lyn;                                                                                                                                                                         
              end;                                                                                                                                                                                  
              else do;                                                                                                                                                                              
                 if countc(lyn,'|')>2;                                                                                                                                                              
                 n=n+1;                                                                                                                                                                             
                 if n ge %eval(&datarow + 1) then do;                                                                                                                                               
                    file print;                                                                                                                                                                     
                    putlog lyn;                                                                                                                                                                     
                    put lyn;                                                                                                                                                                        
                 end;                                                                                                                                                                               
              end;                                                                                                                                                                                  
            run;quit;                                                                                                                                                                               
                                                                                                                                                                                                    
            proc printto;                                                                                                                                                                           
            run;quit;                                                                                                                                                                               
                                                                                                                                                                                                    
            proc import                                                                                                                                                                             
               datafile="%sysfunc(pathname(work))/_tmp2_.txt"                                                                                                                                       
               dbms=dlm                                                                                                                                                                             
               out=&outdsn(drop=var:)                                                                                                                                                               
               replace;                                                                                                                                                                             
               delimiter='|';                                                                                                                                                                       
               getnames=yes;                                                                                                                                                                        
            run;quit;                                                                                                                                                                               
                                                                                                                                                                                                    
            filename _tmp1_ clear;                                                                                                                                                                  
            filename _tmp2_ clear;                                                                                                                                                                  
                                                                                                                                                                                                    
            %utlfkil(%sysfunc(pathname(work))/_tmp1_.txt);                                                                                                                                          
            %utlfkil(%sysfunc(pathname(work))/_tmp2_.txt);                                                                                                                                          
                                                                                                                                                                                                    
       %end;                                                                                                                                                                                        
                                                                                                                                                                                                    
    %mend utl_odstab;                                                                                                                                                                               
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
                                                                                                                                                                                                    
