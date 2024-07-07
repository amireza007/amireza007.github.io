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
------------------------------------------------
//These are all forms that are open in the project,
//also available in "view form" (SHIFT+F12) in c++ builder5
USEFORM("Main1_0_5.cpp", MainForm);
USEFORM("MCFModel1_3.cpp", MCFAlgorithmForm);
USEFORM("OpenPort.cpp", OpenPortForm);
USEFORM("PortAbout.cpp", AboutForm);
USEFORM("PortAGV.cpp", PortAGVForm);
USEFORM("PortContainer.cpp", PortContainerForm);
USEFORM("PortLayout.cpp", PortLayoutForm);
USEFORM("Splash.cpp", SplashForm);
USEFORM("PortBenchmark.cpp", BenchOptionForm);
------------------------------------------------
USEUNIT("PBEAMPP4.cpp");
USEUNIT("PBEAMPP1.cpp");
USEUNIT("PBEAMPP2.cpp");
USEUNIT("PBEAMPP3.cpp");
USEUNIT("Dijkstra.cpp");
USEUNIT("PFLOWUP.cpp");
USEUNIT("TREEUP.cpp");
USEUNIT("READMIN.Cpp");
USEUNIT("PREPAIR.cpp");
USEUNIT("OUTPUT.cpp");
USEUNIT("PSIMPLEX1_3.cpp");
USEUNIT("Mcfutil.cpp");
USEUNIT("PBLA1_3.cpp");
USEUNIT("MCFLIGHT1_0_6.Cpp");
USEUNIT("PSTART.cpp");
```
- Note that the keyword `extern` (explained [here](https://learn.microsoft.com/en-us/cpp/cpp/program-and-linkage-cpp?view=msvc-170)) is used for _private global variables (or external linkage)._ It is not necessary for __free functions__ and __non-const variables__. The `extern` keyword is used in `"MCFModel1_3.cpp"` to reference `MCF_NSA_Solve(...)` method defined in `MCFLIGHT1_0_6.Cpp`.
- Remember that the **`portdatabase`** is in the **`Openport.cpp`**.
- I might need `mcfutil` for the implementation of the nodes

---
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
- <a href="#pbla1_3cpp-and-psimplexcpp">`PBLA`</a> and <a href="#pbla1_3cpp-and-psimplexcpp">`PSIMPLEX1_3`</a> and <a href="#mcfutilcpp-mcflight1_0_6cpp-and-mcfmodel1_3cpp">`MCFLIGHT1_0_6`</a> are closely related! There are some useful comments in them, none of which I understand as I still haven't reviewed the mcf problem and NSA algorithm thoroughly.

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
- `MCFModel1_3` uses a header file, called **`Global.h`**, which contains strtuctures `Port_Buff`, `Container_Buff[Maximum_Container_Jobs]`, `Crane_Buff[Maximum_Number_Cranes]`, `Job_Crane_Buff[Maximum_Number_Cranes]`, `Vehicle_Buff[Maximum_Number_Vehicles]`, `Route_Buff[Maximum_Number_Junctions];`, `Route_Buff2[Maximum_Number_Lanes]`
- The part that uses <a href="#the-job-generator-hcdvrpcpp">`HCDVRP` </a>
```c++
void TMCFAlgorithmForm::Handle_Multi_Load_AGVS();
```
- Important method in `mcfmodel`: `Set_Table4_For_New_Schedule()`
- `Container`, `Vehicle` and `Tour` of `HCDVRP` is used in `Intialize_SA_By_NSA_Solution()`!
- `MCF_NSA_Solve(...)` from `MCFLIGHT1_0_6.cpp` and `Read_NSA_Solution(...)` is used in `RepairGraphModel(...)` in `MCFModel1_3.cpp`.
- `DBGrid4` is the **AGV table** in the **Solution** groupbox.
- **Update**: All ui components are being renamed by their _sensible_ meanings and captions and hints etc.
- `DBGrid5` is the one that \__I think\__ updates the `PortDoneJobTable.db`. The columns of `DBGrid5` are exactly the same as `PortDoneJobTable`.
- So far, I have renamed **Groupboxes, Edits**. I won't rename _all labels_; 
**just the ones in the `MCFModel1_3`.**
- The `BenchOptionForm` recurs multiple times in the source code of `MCFModel1_3`. **Pay Attention to it!**

---
## `openport.cpp`, `portAGV.cpp`, `PortLayout` and `PortContainer.cpp`
- In `openport`, there is the **`PortTable`** that's referenced in `portAGV`, `PortContainer`, `PortLayout`, and `MCFModel1_3`.

---
## <code>HCDVRP.cpp</code>

`HCDVRP` consists of:
- AGV simulation (such as its properties, its actions) + Container simulation.

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

## The Job Generator (`PortLayout.cpp`)

- This is the Famous Job Generator, building the container jobs that should be carried out from a source to a destination.
- It modified the **`PortLayoutTable`** db.
  
---

## Miscellaneous Notes
- `DataSource` type is defined in `OpenPort` file
- `table2` is defined in `PortAGV` file.
- `Defines.h` contains all __constants__ (such as `MAXJOB_0 50, Maximum_Container_Jobs 40` etc.), and `Global_ext.h` contains __simulation variables__ (such as `SOURCEpOINT, dESTpOINT` etc.)
- `PSIMPLEX.h` Only consists of an interface `MCF_primal_net_simplex(MCF_network_p net)`. `PSIMPLEX1_3.cpp` implements it.
- BEA could stand for "Best Entering Arc".

---

## Borland Paradox DB files
- Your reference for tables is [this](https://docwiki.embarcadero.com/Libraries/Sydney/en/Bde.DBTables.TTable)
- `TTables` are descendents of `TDataSets`, which is a virtual class.
- `TTable.post()` writes a modified record to the database and `TTable.delete()` deletes the active record and positions the dataset on the next record.
- To open DB files, download [this](https://github.com/amireza007/DSAGV/blob/main/PDXPlus.exe)
- There are various tables, but **PortLayoutTable** is the one generated with the <a href="#the-job-generator-hcdvrpcpp"> HCDVRP.cpp </a>
- `MCFAlgorithmForm->Table4` and `MCFAlgorithmForm->Table5` are `PortAGVTTable.DB` in the database.
- `PortAGVForm->Table2` and `PortAGVForm->Table1` are `portAGVTable.db` in the database. 
- `PortContainerForm->Table1` and `PortContainerForm->Table2` are `PortContainerTable.db` in the database.
- ` MCFAlgorithmForm->Table1` is `PortDoneJobTable.db` in database.
- **In BDE, `TTable` is the type for creating table objects.**
- Comparing the three tables used mostly in `MCFModel1_3.cpp`, What I think of what the `portAGVTable.db`(AKA `MCFAlgorithmForm->Table4`) consists of is the combination of `portAGVTable.db` and `portContainer.db`, creating a dynamic table for manipulating container job schedules on demand (completely deleting and then re-constructing it).

---

## Helpful C++ Builder5 tricks
- **Routine for builder5 code rename:**
  1. copy component name & paste into `find` of vs code (as regex + (?!\d))
  2. copy caption of the UI component and paste into our program
  3. paste it into the cpp builder5 name and save
  4. paste into the replacement phrase in vs code
  5. find and replace e'm
  6. do the same for the .h file and hit save
  7. close the cpp file from the winxp + reopen it + reload + rebuild project

- To identify a "missing" UI component, use the `view as text` option on the by right-clicking, in order to modify the corresponding `delphi` file.

---
## TODO List:
- [x] `mcfdefs.h`, and `mcf.h`
- [x] `PBLA1_3.cpp`, then `PSIMPLEX1_3.cpp`, and then `TREEUPS.cpp`
- [x] play with Borland's BDE!
- [x] Rename buttons and everything to their associated captions
- [ ] `mcfutil.cpp`, `MCFLIGHT.cpp`, and `MCFModel`.
- [x] The Job Generator (`portLayout.cpp`)
- [x] `OUTPUT.cpp` as it is used by method `MCF_write_solution` in `MCFLIGHT.cpp`.
- [x] the functionalities of `MCFModel`
- [ ] `PREPAIR`
- [ ] `PortBenchmark`, which is mostly a UI thing.
  
## Unanswered Questions:
- What is the use of `MCF_primal_iminus` (and hence `MCF_primal_net_simplex`)? what are jplus and iplus in them?
- Why are there 2 tables pointing to _the same DB_ in `PortAGV.cpp` and `PortContainer.cpp`? (Maybe looking at `Set_Empty_Table4_For_Specific_Port(AnsiString PortNameStr)` in `MCFModel1_3.cpp` might help!)
- Not sure why there are so many `MCFAlgorithmForm->Table4->Delete();` in `MCFModel1_3`? it deletes them, and the loop ends, at the end of the method, `MCFAlgorithmForm->Table4->refresh()`gets called!! why??


## Shortcomings of the DSAGV

- The App resets form sizes after close.
- App opens multiple windows for different forms, why?!
