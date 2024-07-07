---
title: Simulation Data Structures
authors: amireza007
date: 2024-07-08 02:20 +0330
categories: [DSAGV, Source Codes]
tags: [source codes, data structures for DSAGV]
---

This is part of the `HCDVRP.cpp` file:

```c++
struct Container
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
   int        UseConstraintTime ; //  1 if the job concern with pick-up time constraint;
};                                  // -1 if the job concern with put-down time constraint;

struct  Vehicle
{
    int    Capacity;
    String StartLoc;
};

struct  VehileList
{
    int    JobNo;
    int    Action;
    int    ServTime;
};

struct  Trip
{
    String       LocToVisit;
    int          Load;
    int          VehicleTime;
    int          ActualTime;
    VehileList   Contents  [CAPACITY];
};

struct Tour
{
    Trip   *PortTrip;
    int     TotalDemand;
    int     TotalDist;
    int     TotalIdle;
    int     TotalLate;
    int     TotalWait;
    int     Length;
};

struct DistTimeInfo
{
    int    Dist;
    int    Index;
};
```
