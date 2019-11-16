# Basics of Interprocess Communication
## Race Condition

A race condition occurs when a device or system attempts to perform two or more operations simultaneously. It's often the cause of 
'hard-to-find'-errors in the code, as operations - e.g. debugging - can make any indications disappear.

## Disabling Interrupts

  1. Why is it impossible to achieve Mutual Exclusion via disabling interrupts on a multi-core machine?
     >Disabling interrupts is sufficient when it's the only way for the critical section to lose control. On a multi-core machine, 
     a piece of code could never achieve exclusive control over a resource (which would be the definition of a mutual exclusion), 
     because code that's running on different processors can still access the ressource at the same time. Therefore, disabling interruptions
     wouldn't do much in the first place.
  2. Why is it dangerous to give user processes the power to disable interrupts?
     >It would make the user able to do destructive things like executing a malicious program in the kernel mode.

## Peterson's Solution

  1. Play through the two scenarios of the handout of Peterson's solution. Document how it works.
     >First enter_region() gets called. The loop at the bottom will run until the process interested in isn't the same as the one that's 
     waiting anymore. It also accesses shared data. Then, leave_region() gets called which removes the interest from the given process and therefore leaves the 
     critical region.
  2. Play through the scenario which makes the strict alternation approach fail. Document how it fails.
     >When entering the `while(turn != 0)` for the first time, 'turn' will be 0 and therefore not enter. It continues to critical_region()
     and only when it leaves and sets the turn to 1, the loop of the other while can be broken out of, and so on.
  3. What is the meaning of the variable loser in Peterson's solution? When does it have any effect?
     >'loser' stands for the process, that's currently waiting for another process. It gets called when checking if the process interested 
     in is the same that's waiting.
  4. Extend the given functions such they can handle three processes.
     >**Peterson's solution:**
     ```language: c
      #define ME 0
      while (True) 
      {
          enter_region(ME);
          // access shared data
          leave_region(ME);
      }

      #define ME 1
      while (True) 
      {
          enter_region(ME);
          // access shared data
          leave_region(ME);
      }

      #define ME 2
      while (True) 
      {
         enter_region(ME);
        // access shared data
        leave_region(ME);
     }


      void enter_region(int process)
      {
         int other1 = 1 - process;
         int other2 = 2 - process,
         interested[process] = True;
         loser = process;
         while (loser == process && interested[other1] || loser == process && interested[other2] || interested[other2] && interested[other1]) ;
      }
      ```   
      
      >**Strict Alternation:**
     ```language: c
      while (True) 
      {
          while (turn != 0) ;
          critical_region();
          turn = 1;
          noncritical_region();
      }
      
      while (True) 
      {
          while (turn != 1) ;
          critical_region();
          turn = 2;
          noncritical_region();
      }
      
      while (True) 
      {
          while (turn != 2) ;
          critical_region();
          turn = 0;
          noncritical_region();
      }
      ```