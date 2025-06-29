---
authors: amireza007
layout: post
title: DSAGV Guide
date: '2024-05-19 00:26:55 +0330'
categories: [DSAGV]
tags: [Scheduling Algorithms, Vehicle Routing Problem, Minimum Cost Flow, Network Simplex, Automated Guided Vehicles, Integer Programming, AGVs]
---

In this post, we'll examine all important cpp files of DSAGV app, and then continue to explore ways to re-implement them in a state-of-the-art fashion.
In order to make sense of the things I've written below, you need to **read comments before each method definition** in the related cpp source code.

#### The link to the ***trello*** is [here](https://trello.com/b/aES1H8Bv/dsagv).
#### *In the future, I will move into the `doxygen` for better documentation!*
## The structure of <code>PortApp1_0_10.cpp</code>
The `PortApp1_0_10.cpp` file uses the following __forms__ and __translation units__:
```c++
USERES("PortApp1_0_10.res");

//Forms
USEFORM("Main1_0_5.cpp", MainForm);
USEFORM("MCFModel1_3.cpp", MCFAlgorithmForm);//MCFModel1_3 also uses HCDVRP.cpp for Heuristic approach of Simulated Annealing (SA)
USEFORM("OpenPort.cpp", OpenPortForm);
USEFORM("PortAbout.cpp", AboutForm);
USEFORM("PortAGV.cpp", PortAGVForm);
USEFORM("PortContainer.cpp", PortContainerForm);
USEFORM("PortLayout.cpp", PortLayoutForm);
USEFORM("PortBenchmark.cpp", BenchOptionForm);
///////

USEUNIT("PBEAMPP4.cpp");
USEUNIT("PFLOWUP.cpp");
USEUNIT("TREEUP.cpp");

USEUNIT("PSIMPLEX1_3.cpp");
USEUNIT("Dijkstra.cpp");

//stand-alone cpp!
USEUNIT("READMIN.Cpp");

USEUNIT("PREPAIR.cpp");// not included anywhere!

USEUNIT("OUTPUT.cpp");


USEUNIT("PBLA1_3.cpp"); //included as "PBLA.h" in PSIMPLEX

USEUNIT("Mcfutil.cpp");//included in MCFLIGHT and PSIMPLEX
USEUNIT("MCFLIGHT1_0_6.Cpp");
USEUNIT("PSTART.cpp");

USEFORM("Splash.cpp", SplashForm); //The starting screen
//USEUNIT("PBEAMPP1.cpp");
//USEUNIT("PBEAMPP2.cpp");
//USEUNIT("PBEAMPP3.cpp");

```
- Note that the keyword `extern` (explained [here](https://learn.microsoft.com/en-us/cpp/cpp/program-and-linkage-cpp?view=msvc-170)) is used for _private global variables (or external linkage)._ It is not necessary for __free functions__ and __non-const variables__. The `extern` keyword is used in `"MCFModel1_3.cpp"` to reference `MCF_NSA_Solve(...)` method defined in `MCFLIGHT1_0_6.Cpp`.
- Remember that the **`portdatabase`** is in the **`Openport.cpp`**.
- I might need `mcfutil` for the implementation of the nodes

---

## Fundamental Port-related Entities
- Fundamental entities (defined in `Global.h`) are the following:
<details>
  <summary><code>Struct port_buff</code></summary>

{% highlight c++ %}
  String     Port ;
  int        NumberOfBlockYard;
  int        NumberOfWorkingPosition;
  int        NumberOfAGVs  ;
  int        NumberOfJobs  ;
  int        NumberOfJunctions ;
  int        NumberOfLanes ;
  int        NumberOfZones ;
  long       TotalEarlyTimes;
  long       TotalLateTimes;
  long       TotalQCWTimes;
  long       TotalVWTimes;
  long       TotalVTTimes;
  long       TotalDoneJobs;
  long       TotalEarlyJobs;
  long       TotalLateJobs;
  double     TotalDfApAc;
  FILE      *Fout3;
{% endhighlight %}

</details>

<details>
  <summary><code>Struct Container_Buff [Maximum_Container_Jobs]</code></summary>

  {% highlight c++ %}
  String     IDStr         ;
  String     SourcePointStr;
  String     DestPointStr  ;
  long int   ReadyTime     ;
  long int   QCraneTime    ;
  int        Vehicle       ;

  unsigned char Empty      ;
  unsigned char Done       ;
  {% endhighlight %}

</details>
<details>
  <summary markdown="span"><code>Struct Vehicle_Buff</code></summary>

  {% highlight c++ %}
   String StartLoc;
   int    Last_Completed_Temp;
   int    Number_Of_Jobs;
   int    Last_Completed_Time;
   int    Number_Of_Done_Jobs;
   int    Lane_No            ; // <=Number_of_Lanes
   int    Time_in_Lane       ;
   int    Number_of_Lanes    ;
   int    Lane   [Maximum_Number_Lanes];
   int    Cost_Temp;
   int    PreJob_Temp;
   int    No_Job_Temp;
  {% endhighlight %}

</details>

<details>
  <summary markdown="span"><code>Struct Route_Buff</code></summary>

  {% highlight c++ %}
   int    Junction ;
   String Location ;
   int    NextJunc1;
   int    NextLane1;
   int    DurationLane1;
   int    NextJunc2;
   int    NextLane2;
   int    DurationLane2;
   int    NextJunc3;
   int    NextLane3;
   int    DurationLane3;
  {% endhighlight %}

</details>

<details>
  <summary markdown="span"><code>Struct Route_Buff2</code></summary>
  
  {% highlight c++ %}
   int    Duration;
   int    Busy    ;
   int    Vehicle ; // [Maximum_NAGV_In_Lan];
  {% endhighlight %}

</details>
-----

## <code>mcfdefs.h</code>, <code>mcf.h</code>
- The `mcfdefs.h` contains the **fields** (parameters and nodes) of the `MCFModel` and `mcf.h` contains **methods**. 
Examples of the components of `mcfdefs.h`: (A description of Nodes can be found [here](https://github.com/amireza007/amireza007.github.io/blob/master/assets/mcfdefs.pdf))
```c++ 
struct MCF_node{ ///MCF_node_p is the alias (or typeof) of MCF_node
    int indent;
    int pre_indent;
    MCF_node_p child;
    MCF_node_p right_sibling;     
    MCF_node_p left_sibling;     
    ....
}
```

- Examples of the components of `mcf.h`: 
```c++
extern long MCF_write_intermediate2(MCF_network_p net);
extern long MCF_primal_start_artificial (MCF_network_p net);
extern long MCF_primal_net_simplex (MCF_network_p net);
```
**Please note that the comments ("_documentation_") are actually good and pretty important in these two cpp files!**

---
## <code>PBLA1_3.cpp</code>, <code>PXIMPLEX1_3.cpp</code>, <code>TREEUPS.cpp</code>, <code>MCFLIGHT1_0_6.cpp</code>
- `PBLA` and `PSIMPLEX1_3` and `MCFLIGHT1_0_6` are closely related! There are some useful comments in them, none of which I understand as I still haven't reviewed the mcf problem and NSA algorithm thoroughly.

---
## <code>PBLA1_3.cpp</code> and <code>PSIMPLEX.cpp</code>
- The `PBLA` (which most likely stands for "problem of best leaving arc") only contains the _mysterious_:
```c++
MCF_node_p MCF_primal_iminus(MCF_flow_p delta,
                             long       *xchange,
                             MCF_node_p iplus,
                             MCF_node_p jplus,
                             MCF_node_p *w     );
```
- The `PSIMPLEX` (The bigger cpp file with `"mcfdefs.h`",`"pbeadef.h"`,`"pbea.h"`,`"pbla.h"`,`"pflowup.h"`,`"treeup.h"`,`"mcfutil.h"`) only contains the _mysterious_:
```c++
long MCF_primal_net_simplex(MCF_network_p net);
```
- `TREEUP` is used in `PSIMPLEX1_3`.

---
## <code>Mcfutil.cpp</code>, <code>MCFLIGHT1_0_6.cpp</code>, and <code>MCFModel1_3.cpp</code>
- portappdatabase is updated within MCFModel1_3.cpp.
- The part that uses`HCDVRP` </a>
  
```c++
void TMCFAlgorithmForm::Handle_Multi_Load_AGVS();
```
- Important method in `mcfmodel`: `Set_Table4_For_New_Schedule()`
- `Container`, `Vehicle` and `Tour` of `HCDVRP` is used in `Intialize_SA_By_NSA_Solution()`!
- `MCF_NSA_Solve(...)` from `MCFLIGHT1_0_6.cpp` and `Read_NSA_Solution(...)` is used in `RepairGraphModel(...)` in `MCFModel1_3.cpp`.
- `DBGrid4` is the **AGV table** in the **Solution** groupbox.
  
- `DBGrid5` is the one that \__I think\__ updates the `PortDoneJobTable.db`. The columns of `DBGrid5` are exactly the same as `PortDoneJobTable`.

- The `BenchOptionForm` recurs multiple times in the source code of `MCFModel1_3`. **Pay Attention to it!**

---
## `openport.cpp`, `portAGV.cpp`, `PortLayout` and `PortContainer.cpp`
- In `openport`, there is the **`PortTable`** that's referenced in `portAGV`, `PortContainer`, `PortLayout`, and `MCFModel1_3`.

## Job generator (Generate Button in MCFModel1_3)
- It exists as an `MCFModel1_3.cpp` method:
  ```c++
  void __fastcall TMCFAlgorithmForm::Generate_Button11Click(TObject *Sender)
  ```
- If you right click on **Port and Container Job (static)** Form, you will find a `PopupMenu1` with two items `Generate Schedule` and `Reset Schedule`. `Generate Schedule` Event points to the above cpp line! WHY?? 

---
## <code>HCDVRP.cpp</code> Heuristic approach using Simulated Annealing

`HCDVRP` consists of:
- This file uses the already known parameters of the `MCFModel1_3` such as Container Jobs and Time windows. 
- It is also very much dependent of some kind of an initial solution. This initial solution can be attained by the use of `MCF_NSA_Solution` method in `MCFModel1_3`.
<br>  
Simulation parameters are as follows:

```c++
struct Container:
{
    int        JobNo      ;
    int        Demand     ;
    int        AppointmentTime;  // ReadyTime
    int        QCraneTime ;
    int        DueTime    ;
    int        ServiceTime;
    int        SourceDone ;        // 1 if the job has been pick up, otherwise 0
    int        DestDone   ;        // 1 if the job has been put down, otherwise 0
    String     SourceLocation;
    String     DestLocation  ;
    int        UseConstraintTime ;
};
struct  Vehicle
{
	int    Capacity;
        String StartLoc;
};
//Three Important properties in this file, used in other places.
Container *CJob;
Vehicle  *AGV;
Tour     *TAGV,*TempT,*BestT;
```

- It also generates __Trips, Tours,__ and __Destination information__.
- It's declared as `VRP_2` in `MCFModel1_3`.

---

## `PortLayout.cpp`

- I really don't understand what the use of this *really* is. I guess, it just **loads information about ports (using `openport.cpp`) and the distance of each AGV and 
- It modifies the **`PortLayoutTable`** db.
- It then shows them in the "Route Table", specifying consecutive junction location and *TimeLanes*(??)

---

## Miscellaneous Notes
- `DataSource` type is defined in `OpenPort` file
- `table2` is defined in `PortAGV` file.
- `Defines.h` contains all __constants__ (such as `MAXJOB_0 50, Maximum_Container_Jobs 40` etc.), and `Global_ext.h` contains __simulation variables__ (such as `SOURCEpOINT, dESTpOINT` etc.)
- `PSIMPLEX.h` Only consists of an interface `MCF_primal_net_simplex(MCF_network_p net)`. `PSIMPLEX1_3.cpp` implements it.
- BEA could stand for "Best Entering Arc".

---

## Borland Paradox DB files
- **For edditting Paradox DB files**: use `pdxEditor` in the GDrive.
- Your reference for tables is [this](https://docwiki.embarcadero.com/Libraries/Sydney/en/Bde.DBTables.TTable)
- `TTables` are descendents of `TDataSets`, which is a virtual class.
- `TTable.post()` writes a modified record to the database and `TTable.delete()` deletes the active record and positions the dataset on the next record.
- To open DB files, download [this](https://github.com/amireza007/DSAGV/blob/main/PDXPlus.exe)
- There are various tables, but **PortLayoutTable** is the one generated with the<u> HCDVRP.cpp</u> </a>
- `MCFAlgorithmForm->Table4` and `MCFAlgorithmForm->Table5` are `PortAGVTTable.DB` in the database.
- `PortAGVForm->Table2` and `PortAGVForm->Table1` are `portAGVTable.db` in the database. 
- `PortContainerForm->Table1` and `PortContainerForm->Table2` are `PortContainerTable.db` in the database.
- ` MCFAlgorithmForm->Table1` is `PortDoneJobTable.db` in database.
- **In BDE, `TTable` is the type for creating table objects.**
- Comparing the three tables used mostly in `MCFModel1_3.cpp`, What I think of what the `portAGVTable.db`(AKA `MCFAlgorithmForm->Table4`) consists of is the combination of `portAGVTable.db` and `portContainer.db`, creating a dynamic table for manipulating container job schedules on demand (completely deleting and then re-constructing it).

---

## More on Databases used in the program
<u>9 Databases</u> is used in the program. We'll talk more about from which files they are **created** and **accessed** and **modified**!
1. `AlgorithmTable.db`
2. `PortAGVTable.db` and `PortAGVTTable.db`
3. `PortContainerTable.db`: 
4. `PortDoneJobTable.db`
5. `PortLayoutTable.db`
6. `PortRouteTable.db`: It specifies **Location of Junctions (تقاطع)** in each port. 
     - Modified in: `PortLayout.cpp` by the method `BitBtn2Click`
7. `PortStatusJobTable.db`
8. `PortTable.db`

---




## Deficiencies of the DSAGV
- The App resets form sizes after close.
- App opens multiple windows for different forms, why?!

## Some C++ Builder 5 tips:
-  when opening a builder project, move cursor on a resource and right click and select `open file under cursor` to open the corresponding file, in place!
- a way of printing bugs! **stupid software!!!!!**
  ```c++
  std::stringstream ss;
  ss << Port_Buff.NumberOfAGVs;
  std::string str = ss.str();
  Application->MessageBox(str.c_str(),"bug",MB_OK);
  ```
 
## Unanswered Questions:
- What is the use of `MCF_primal_iminus` (and hence `MCF_primal_net_simplex`)? what are jplus and iplus in them?
- Why are there 2 tables pointing to _the same DB_ in `PortAGV.cpp` and `PortContainer.cpp`? (Maybe looking at `Set_Empty_Table4_For_Specific_Port(AnsiString PortNameStr)` in `MCFModel1_3.cpp` might help!)
- Not sure why there are so many `MCFAlgorithmForm->Table4->Delete();` in `MCFModel1_3`? it deletes them, and the loop ends, at the end of the method, `MCFAlgorithmForm->Table4->refresh()`gets called!! why??
- (***Very Important Question):*** How **container jobs** **database (PortContainerTable.db)** is created and updated??
- It's interesting, When you run the installed app, the solutions are displayed. However, when you compile the version 10, no solutions are appeared!

## Update 1/23/2025:
- The method associated to `Button11`, which is **Generate** button in static fashion, is the **Heart of the `MCFModel1_3.cpp` file** containing the schedule generation for the job and connecting all previously written methods in `MCFModel1_3.cpp`.
- Similarly, there is `Button7Click`, which is the intention of Dr. Rashidi to give out the source code. 
- In **static** fashion, the data (container jobs, the network graph and number of QCs and AGVs) are fed into a method named `MCF_NSA_Solve` in the button11click method of `MCFModel1_3.cpp` . The output is the either 0,1,-1, with 0 being success. 
- **The reason why we don't see generated jobs:** because they are not generated as there is a line in the `MCFModel1_3`, in **button11Click** method, written as: 
  ```c++
  bool b = PortContainerForm->Table1->Locate("Portname", VarArrayOf(locvalues, 0), Opts);
  ```
  This code basically tell the program to not generate the jobs, if the port name exists in the `PortContainerTable.DB`, which is not an interesting trait!! <br>At least the original coder could've written some comments, telling "If you like to generate it, set this bool to false yourself". **NOT COOL!**

## The structure of MCF_Network:
<br> Three important components: **nodes, arcs, network**

<details>
  <summary><b> <code>MCF_node</code></b></summary>
  {% highlight c++ %}
  struct MCF_node
  {
    /** Ident flag.
     *
     * This variable is only used to identify status of each node.
     */
    int ident;

    /** Pre_Ident flag.
     *
     * This variable is only used to identify previous status of each node.
     */
    int pre_ident;

    /** Node identifier.
     *
     * This variable is only used to assign some identification to each node.
     * Typically, as for the DIMACS format, nodes are indexed from 1 to n, where
     * n denotes the number of nodes.  */
    long number;


    /** predecessor node.
     *
     */
    MCF_node_p pred;


    /** First child node.
     *
     */
    MCF_node_p child;


    /** Next child of predecessor.
     *
     */
    MCF_node_p right_sibling;     


    /** Previous child of predecessor.
     *
     */
    MCF_node_p left_sibling;     
    

    /** Number of nodes (including this one) up to the root node.
     *
     */
    long subtreesize; 
    

    /** The node's basic arc.
     *
     */
    MCF_arc_p basic_arc; 
    

    /** Orientation of the node's basic arc.
     *
     * This variable stands for the orientation of the node's basic arc.  The
     * value UP (= 1) means that the arc points to the father, and the
     * value DOWN (= 0) means that the arc points from the father to this node.
     *
     */
    long orientation; 


    /** First arc of the neighbour list of arcs leaving this node.
     *
     */
    MCF_arc_p firstout;
    

    /** First arc of the neighbour list of arcs entering this node.
     *
     */
    MCF_arc_p firstin;
    

    /** Supply/Demand $b_i$ of this node.
     *
     * A node $i$ is called a supply node, a demand node, or a transshipment
     * node depending upon whether $b_i$ is larger than, smaller than, or equal
     * to zero, respectively.
     * */
    MCF_flow_t balance;  
    

    /** Dual node multipliers.
     *
     * This variable stands for the node potential corresponding with the flow
     * conservation constrait of this node.
     *
     */
    MCF_cost_t potential; 
    

    /** Flow value of the node's basic arc.
     *
     */
    MCF_flow_t flow;
    

    /** Temporary variable.
     *
     * This is a temporary variable, which you can use as you like.
     *
     */
    long mark;
  };
  {% endhighlight %}
</details>

<details>
  <summary><b> <code>MCF_arc</code></b></summary>
  {% highlight c++ %}
  struct MCF_arc
{
    /** Arc Previous status.
     *
     * This variable shows the previous arc status. Feasible is BASIC
     * (for basic arcs), MCF\_AT\_LOWER\_BOUND (nonbasic arcs set to lower
     * bound), MCF\_AT\_UPPER\_BOUND (nonbasic arcs set to the upper bound),
     * MCF\_AT\_ZERO (nonbasis arcs set to zero), or FIXED (arcs fixed to
     * zero and being not considered by the optimization).
     *
     */
    long pre_ident;
    /** Tail node.
     *
     */
    MCF_node_p tail;


    /** Head node.
     *
     */
    MCF_node_p head;


    /** Next arc of the neighbour list of arcs leaving the tail node.
     *
     */
    MCF_arc_p nextout;


    /** Next arc of the neighbour list of arcs entering the head node.
     *
     */
    MCF_arc_p nextin;


    /** Arc costs.
     *
     * This variable stands for the arc cost (or weight).
     *
     * Our primal feasible starting basis consists just of artificial arcs
     * (corresponding to a slack basis), and all originally defined arcs are
     * first nonbasic at their lower bounds. The costs of the artificial arcs
     * are set to MAX\_ART\_COST, which is defined in the file mcfdefs.h. It is
     * easy to see that any feasible and optimal solution with artificial arcs
     * is also optimal and feasible for the original problem without artificials
     * iff no artifical arc yields a nonzero flow value. If, however, a solution
     * contains an artificial arc with positive flow, the original problem is
     * either indeed infeasible or the MAX\_ART\_COST is just too small compared
     * to the cost coefficients of the original arcs. If the latter is the case,
     * increase MAX\_ART\_COST, but we also strongly recommend to use then
     * floating point arithmetic!
     *  */
    MCF_cost_t cost;


    /** Arc upper bound.
     *
     * This variable stands for the arc upper bound value. Note that an
     * unbounded upper bound is set to UNBOUNDED, which is defined in the file
     * mcfdefs.h. Per default, UNBOUNDED is set to $10^9$.  Note, this value may
     * be too small for your purposes, and you should increase it appropriately.
     * However, we strongly recommend to use then floating point arithmetic
     * (define MCF\_FLOAT)!
     *
     */
    MCF_flow_t upper;


    /** Arc lower bound.
     *
     * This variable stands for the arc lower bound value. Note, this variable
     * is only active if the MCF\_LOWER\_BOUNDS variable is defined! An negative
     * unbounded lower bound is set to -UNBOUNDED, see also the arc upper bound.
     *
     */
#ifdef MCF_LOWER_BOUNDS
    MCF_flow_t lower;
#endif


    /** Arc flow value.
     *
     * This variable stands for the arc's flow value. Note that the flow value
     * is not set within the main (primal or dual) iteration loop; actually, it
     * can only be computed using the function primal\_obj().
     *
     */
    MCF_flow_t flow;


    /** Arc status.
     *
     * This variable shows the current arc status. Feasible is BASIC
     * (for basic arcs), MCF\_AT\_LOWER\_BOUND (nonbasic arcs set to lower 
     * bound), MCF\_AT\_UPPER\_BOUND (nonbasic arcs set to the upper bound),
     * MCF\_AT\_ZERO (nonbasis arcs set to zero), or FIXED (arcs fixed to 
     * zero and being not considered by the optimization).
     *
     */
    long ident;
};
{% endhighlight %}
</details>


<details>
  <summary><b> <code>MCF_network</code></b></summary>

  {% highlight c++ %}
  struct MCF_network
{
    /** Number of nodes.
     *
     * This variable stands for the number of originally defined nodes without
     * the artificial root node.
     *
     */
    long n;


    /** Number of arcs.
     *
     * This variable stands for the number of arcs (without the artificial slack
     * arcs).
     *
     */
    long m;

    /** Options for candidate arc group.
     *
     * This variable will be set to 0 (Fixed candidate group) or 1 (Random Candidate Group)
     *
*/
    int algorithm_opt;

    /** Block size of the model.
     *
     * This variable will be set to the size of block in arcs calssification.
     *
*/
    int block_size;

    /** CPU time to solve the model.
     *
     * This variable will be set to the consumed time to solve the moedl, iff the model is fesaible.
     *
*/
    double cpu_time;

    /** maximum cost of arcs in the model.
     *
     * This variable will be set to maximum cost in the model.
     *
*/
    long max_cost;

    /** the arc in maximum cost.
     *
     *
     */
    MCF_arc_p max_cost_arc;


    /** Primal unbounded indicator.
     *
     * This variable is set to one iff the problem is determined to be primal
     * unbounded.
     *
*/
    long primal_unbounded;


    /** Dual unbounded indiciator.
     *
     * This variable is set to one iff the problem is determined to be dual
     * unbounded.
     *
     */
    long dual_unbounded;


    /** Feasible indicator.
     *
     * This variable is set to zero if the problem provides a feasible solution.
     * It can only be set by the function primal\_feasible() or dual\_feasible()
     * and is not set automatically by the optimization.
     *
     */
    long feasible;

    /** Costs of current basis solution.
     *
     * This variable stands for the costs of the current (primal or dual) basis
     * solution. It is set by the return value of primal\_obj() or dual\_obj().
     *
     */
    double optcost;


    /** Vector of nodes.
     *
     * This variable points to the $n+1$ node structs (including the root node)
     * where the first node is indexed by zero and represents the artificial
     * root node.
     * 
     */
    MCF_node_p nodes;


    /** First infeasible node address.
     *
     * This variable is the address of the first infeasible node address,
     * i.\,e., it must be set to $nodes + n + 1$.
     *
     */
    MCF_node_p stop_nodes;


    /** Vector of arcs.
     *
     * This variable points to the $m$ arc structs.
     *
     */
    MCF_arc_p arcs;


    /** First infeasible arc address.
     *
     * This variable is the address of the first infeasible arc address, i.\,e.,
     * it must be set to $nodes + m$. 
     *
     */
    MCF_arc_p stop_arcs;


    /** Vector of artificial slack arcs.
     *
     * This variable points to the artificial slack (or dummy) arc variables and
     * contains $n$ arc structs.  The artificial arcs are used to build (primal)
     * feasible starting bases. For each node $i$, there is exactly one dummy
     * arc defined to connect the node $i$ with the root node.
     * */
    MCF_arc_p dummy_arcs;


    /** First infeasible slack arc address.
     *
     * This variable is the address of the first infeasible slack arc address,
     * i.\,e., it must be set to $nodes + n$. 
     *
     */
    MCF_arc_p stop_dummy; 


    /** Iteration count.
     *
     * This variable contains the number of main simplex iterations performed to
     * solve the problem to optimality.
     * 
     */
    long iterations;


    /** Dual pricing rule.
     *
     * Pointer to the dual pricing rule function that is used by the dual
     * simplex code.
     * */
    MCF_node_p (*find_iminus) ( long n, MCF_node_p nodes, 
                                MCF_node_p stop_nodes, MCF_flow_p delta );//this is not used anywhere in the codes!
    /** Primal pricing rule.
     *
     * Pointer to the primal pricing rule function that is used by the primal
     * simplex code.
     * */
    MCF_arc_p (*find_bea) ( MCF_arc_p max_cost_arc, int algorithm_opt,long m, MCF_arc_p arcs, MCF_arc_p stop_arcs,
                            MCF_cost_p red_cost_of_bea );
};
{% endhighlight %}
</details>

----

## The Model: MCF_AGV graph:

### symbols and variables:
- $$t_i$$: appointment time of job i at the quayside. 

## The Dynamic Approach
### How dynamic approach implemented
- Dynamic Approach (DA) uses <ins> `Insert_Containers_To_Table(int CN)`</ins> to generate containers, which is **odd** with consideration of using the `JobGenerator` (AKA `button11click`) method.  
- Both Static and Dynamic write into **table2**, which accesses`portContainerTable.db`. 
### Dynamic approach Bugs
---

## TODO List:
- [X] Bug in `Honk Kong` part in `MCFModel1_3` in `Port_Names_Static_ListBoxClick` method.<br>
  - **Fix**: changed the `Maximum_Number_Vehicles` from 50 to 250
- [ ] **What optimzation problem is this program trying to solve?** -> Read Ch 5 of *Port Automation* book by Rashidi.
- [ ] Fixing Job generator Table view: When `Edit1` is set in the `MCFModel1_3` form, the generatod container jobs aren't shown. <br> 
- [ ] The **key violation** error is caused by the program creating the same container jobs with the initials `C-BS-`. Fix that by clearing the `PortContainerTable.DB` first.
- [ ] In order to write doxygen, you should write **documentation comments** inside source codes.
- [ ] **Urgent**: line 2237, `NumContainers` looks bad! (it's related to `NumberOfContainers` in `AGVTTable.DB`, which doesn't make sense, if you check the DB.)
- [ ] `PBEAMPP1,PBEAMPP2,PBEAMPP3,PBEAMPP4`, which all stand for **Primal Basis Entering Arc, Multiple Partial Pricing** and is part of **SPEC2000** testing (written by A. Loebel), are the same cpps, just differing in the value of **K** and **B**. Could be easilly fixed.
- [ ] `PBEAMPP`, `PBLA`, `PSIMPLEX`, `mcf` (implementations of MCF are in `mcfutil`), `PFOLLOWUP`, `TREEUP`,`PSTART`, `MCFLIGHT1_0_6` (NSA solving)