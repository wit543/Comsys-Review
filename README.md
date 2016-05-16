# Comsys Review

## Cache
 - Software tool: pintools
 - Type of cache:
  - icache =  instruction cache
  - Dcache = data cache
 - code
  - List all command
  
    32bit
    ```
     sudo ../../../pin -t obj-ia32/icache.so -- /bin/ls 
    ```
   
    64bit
    ```
     sudo ../../../pin -t obj-intel64/icache.so -- /bin/ls 
    ```
  - Run
     
    32bit
    ```
     sudo ../../../pin -t obj-ia32/icache.so -- ./ชื่อไฟล์
    ```
   
    64bit
    ```
     sudo ../../../pin -t obj-intel64/icache.so -- ./ชื่อไฟล์
    ```
 - Type of tool
  - **Jumpmix.co**: tracing instructions
  - **Inscount0.co**: count instruction 
  - **cjtrace.so**: assembly listing
  - **dcache.co**: data cache hits/misses
  - **opcodemix.co**: instruction counts by instruction type
  - **pinatrace.co**:  memory reference of your matrix program here by recording the address of the memory and whether it is the read or write
 - Question
  - What is the CPI for your matrix code from Perf tool?
   ```
   perf stat -d ./ชื่อไฟล์
   ```
 - Option
  - **-z** : ignore the size of each reference
  - **-ns**: ignore all memory writes (stores)
 - output:
  ```
  
    # DCACHE configuration [c = 32KB, b = 128B, a = 8]
    #
    # DCACHE stats
    #
    # L1 Data Cache:
    # Load-Hits:           	171768   98.58%
    # Load-Misses:           	2479	1.42%
    # Load-Accesses:       	174247  100.00%
    # 
    # Store-Hits:           	72264   99.63%
    # Store-Misses:           	271	0.37%
    # Store-Accesses:       	72535  100.00%
    # 
    # Total-Hits:          	244032   98.89%
    # Total-Misses:          	2750	1.11%
    # Total-Accesses:      	246782  100.00%
    
  ```
