![Comsys](https://i.giphy.com/3o6Ei3ezR3iOLzPHpe.gif)
# Comsys Review

## **Cache**
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
    
    C = The total size
    B = Line size
    A = Ways
  ```
 - picture
 
  ![comsys](/comsys.png)

##**system c**
- **Main.cpp**
  ```C++
   #include <systemc.h>
   
   #include "stim.h"
   #include "check.h"
   #include "seq_det.h"
   
   int sc_main(int argc, char* argv[])
   {
   	sc_signal<bool> in,out; // initialize a single
   	sc_clock clk("clk", 10, SC_NS, 0.5);   // Create a clock signal
   
   
   	sc_trace_file *fp;   //trace file
   	fp = sc_create_vcd_trace_file("seq_det"); // init trace file
   	fp->set_time_unit(1, SC_PS); // set time unit to  pico second
   	// bind each variable to trace
   	sc_trace(fp, clk, "clk");
   	sc_trace(fp, in, "S");
   	sc_trace(fp, out, "Z");
   
   	
   	stim sim("stim"); // create a module
    //bind each variable to the module
   	sim.clk(clk);
   	sim.s(in);
   	
   	seq_det seq("seq_det");
   	seq.sin(in);
   	seq.clk(clk);
   	seq.z(out);
   
   	check ch("check");
   	ch.clk(clk);
   	ch.s(out);
   
   	sc_start(1010, SC_NS); // start the simulation 
   	
   	return 0;// Return OK, no errors.
   }
  ```
-**Seq_det.h**
  ```
  #ifndef seq_det
  #define SEQ_DET
  
  #include <systemc.h>
  
  SC_MODULE(seq_det) {
  	/*input and output signal*/
  	sc_in_clk clk;
  	sc_in<bool> sin;
  	sc_out<bool> z;
  	sc_int<4> current_state, next_state;
  	
  	void change_state() {
  		/* for writing to a signal variable, we can use many way
  			z= false;
  			z.write(false);
  		    For reading value
  			z.read();
  		*/
  	}
  	// constructor
  	SC_CTOR(seq_det) {
  		SC_METHOD(change_state); // use change_state as a method
  		// SC_THREAD(change_state); // use change_state as a thread
  		
  		// add a signal to sensitive for listen change (if signal change the module will run)
  		// if a datatype is sc_in_clk need to call pos()
    sensitive << clk.pos();
    // if it is a sc_in or sc_out
    sensitive <<z;
    		
    }
  };
  #endif
  ```
##**Thread executor service**
- **ExecutorService**
  ```java
   //วิธีใช้ ExecutorServiceเบื้องต้น
   import java.util.concurrent.Callable;
   import java.util.concurrent.ExecutionException;
   import java.util.concurrent.ExecutorService;
   import java.util.concurrent.Executors;
   public class ExeTest{
     public static void main (String[] args){
   //เอาจำนวนThreadจากprocessor (อันนี้ไม่แน่ใจนะตามอาจารย์ ทำไม+1ไม่รู้555)
       int thread_num = Runtime.getRuntime().availableProcessors() + 1;
   // ประกาศExecutorServiceที่ใช้รันcallable
       ExecutorService executorService = Executors.newFixedThreadPool(thread_num);
   // ยัดcallableให้executor serviceมัน ทำ (submit)
       Datatype a = executorService.submit(new Callable<Datatype>() {
   	public Datatype call() {
   		 //สิ่งที่จะเอามาทำเขียนในนี้
   		return ??; // รีเทิร์นค่าได้ด้วยนะ *0* ←	return แล้วไปไหน?? <- returnออกสู่โลกภายนอกไง (เวลาgetก็จะได้ค่านี้แหละ)
   	}
   	}).get(); // ถ้าอยากได้ค่าที่รีเทิร์นออกมา ก็.get()หลังsubmitเอา
     }
   }
 
  ```
- **ForkJoinPool**
  ```java
   //วิธีใช้ ForkJoinPool
   import java.util.concurrent.ForkJoinPool;
   import java.util.concurrent.RecursiveTask;
   
   public class ForkTest{
     public static void main(String[] args){
       int thread_num = Runtime.getRuntime().availableProcessors() + 1;//เอาจำนวนThreadจากprocessor (อันนี้ไม่แน่ใจนะตามอาจารย์ ทำไม+1ไม่รู้555)
       ForkJoinPool forkJoinPool = new ForkJoinPool(thread_num); // ประกาศ ForkJoinPool
       TaskTest task = new TaskTest(size); //ประกาศTaskของเราอย่าลืมใส่sizeละว่ามันจะทำกี่รอบ
       forkJoinPool.invoke(task);//เอาtaskไปทำ
       Datatype result = task.result; //ได้ผลลัพธ์มาและ ตอนมันเสร็จ
     }
   }
   //อันนี้เป็นtaskอะ conceptอันนี้ประมาณ divide and conquer 
   class TaskTest extends RecursiveTask<Datatype>{
     private int num; //เอาไว้นับว่าตอนนี้แบ่ง divide ไปเท่าไหร่แล้ว
     public Datatype result; //ไว้เก็บresultที่เราได้มา
     public TaskTest(int num){
       this.num=num;
     }
     @Override
   	protected Datatype compute() {
   	  if (num<2) { //divideสุดทางและอันนี้เหลือแค่อันเดียว
   	    result = ??; //สุดทางและ ก็ตั้ง resultให้เป็นค่าที่ต้องการ
   	  }
   	  else {
   	    TaskTest task1 = new TaskTest(num/2); // divide 
   	    TaskTest task2 = new TaskTest(num/2); // divide 
   	    task1.fork(); // จัดการfork task1 ให้ไปทำๆๆๆๆ 
   	    Datatype result2 = task2.compute(); //เอาค่าของtask2มาcompute;
   	    Datatype result1  = task1.join(); //เอาค่าของtask1หลังโดนไล่ไปทำมา
   	    result = result2+result1; //อันนี้ถ้ามาcombineแบบนี้ได้นะ 
   	  }
   	  return result;
   	}
   }
 
  ```
