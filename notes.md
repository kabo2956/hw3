#hw 3
Kaleb Bodisch Hw3



MY machine:
Number of MPI processes 1 Processor names  kaleb-Aspire-E5-575G
Triad:         7800.9374   Rate (MB/s)
Number of MPI processes 2 Processor names  kaleb-Aspire-E5-575G kaleb-Aspire-E5-575G
Triad:        10291.3237   Rate (MB/s)
Number of MPI processes 3 Processor names  kaleb-Aspire-E5-575G kaleb-Aspire-E5-575G kaleb-Aspire-E5-575G
Triad:        10293.2384   Rate (MB/s)



The below examples are just a few examples of tests I ran on my machine to determine which ksp solver and preconditioners worked best. 

At the bottom of the document are the results of the summit runs.

Example 19

mpiexec -n 1 ./ex19 -da_refine 5 -ksp_type gmres -pc_type gamg -log_view -ksp_monitor
   13 KSP Residual norm 6.106195823851e-12
Number of SNES iterations = 4
Time (sec):           3.099e+00
THIS is slower with two processes



mpiexec -n 1 ./ex19 -da_refine 5 -ksp_type gmres -pc_type lu -log_view -ksp_monitor
    1 KSP Residual norm 3.266849762336e-19
Number of SNES iterations = 2
Time (sec):           1.061e+00


mpiexec -n 1 ./ex19 -da_refine 5 -ksp_type gmres -pc_type mg -log_view -ksp_monitor
    6 KSP Residual norm 3.473188095271e-11
Number of SNES iterations = 2
Time (sec):           5.789e-01


mpiexec -n 1 ./ex19 -da_grid_x 4 -da_grid_y 4 -da_refine 6 -ksp_type gmres -pc_type lu -log_view -ksp_monitor
    1 KSP Residual norm 2.369920618057e-18
Number of SNES iterations = 2
Time (sec):           7.689e+00



mpiexec -n 1 ./ex19 -da_grid_x 4 -da_grid_y 4 -da_refine 6 -ksp_type gmres -pc_type mg -log_view -ksp_monitor
    6 KSP Residual norm 1.659685755154e-15
Number of SNES iterations = 3
Time (sec):           2.874e+00
THIS Is slower when you go to two processes due to the small grid size chosen.



exec -n 1 ./ex48 -M 64 -log_view -ksp_monitor -ksp_type gmres -pc_type lu
    1 KSP Residual norm 5.553245248308e-20
Solution statistics after solve: Full
CONVERGED_FNORM_RELATIVE: Number of SNES iterations = 7,
Time (sec):           4.655e+00





mpiexec -n 1 ./ex48 -M 64 -log_view -ksp_monitor -ksp_type gmres -pc_type mg
   15 KSP Residual norm 1.774968208860e-11
Solution statistics after solve: Full
CONVERGED_FNORM_RELATIVE: Number of SNES iterations = 7
Time (sec):           1.342e+00



in parallel
mpiexec -n 2 ./ex48 -M 64 -log_view -ksp_monitor -ksp_type gmres -pc_type mg
   18 KSP Residual norm 7.803410876486e-12
Solution statistics after solve: Full
CONVERGED_FNORM_RELATIVE: Number of SNES iterations = 7
Time (sec):           1.084e+00


mpiexec -n 2 ./ex48 -M 64 -log_view -ksp_monitor -ksp_type_gmres_modified_gramschmidt -pc_type bjacobi
   29 KSP Residual norm 1.074224249087e-11
Solution statistics after solve: Full
CONVERGED_FNORM_RELATIVE: Number of SNES iterations = 7
Time (sec):           6.935e-01

After talking to professor in office hours, I decided to test the functions a little bit differently than some of the above tests.






SUMMIT RUNS

summit Intel(R) Xeon(R) CPU E5-2680 v3 @ 2.50GHz
np  speedup
1 1.0
2 1.87
3 2.39
4 3.15
5 3.85
6 4.6
7 5.33
8 6.02
9 6.29
10 7.03
11 7.69
12 8.3
13 8.55
14 9.06
15 8.91
16 9.61
17 9.72
18 10.19
19 10.0
20 10.47
21 10.42
22 10.82
23 10.7
24 11.04


Make streams seemed to scale consistently till 18 processes.  


Based off of tests done on my personal machine, I decided to run my tests using a combination ksp_type gmres and cg. Using pc_types bjcabo and mg.

I decided against using lu decomposition in order to simplify packages thatneeded to be downloaded during configuration

When running I decided to only change the grid size for testing scaling instead of the refinement or a combination of grid size and refinement

For the baseline tests I did two tests for each configuration in order to better understand scaling on summit.

baseline tests for ex 19:

GMRES AND BJACOBI
-bash-4.2$ mpiexec -n 18 ./ex19 -da_grid_x 200 -da_grid_y 200 -ksp_type gmres -pc_type bjacobi -log_view | grep "Time (sec):"
Time (sec):           1.868e+00      1.00002   1.868e+00
-bash-4.2$ mpiexec -n 18 ./ex19 -da_grid_x 400 -da_grid_y 400 -ksp_type gmres -pc_type bjacobi -log_view | grep "Time (sec):"
Time (sec):           2.241e+01      1.00000   2.241e+01


GMRES AND MG
-bash-4.2$ mpiexec -n 18 ./ex19 -da_grid_x 400 -da_grid_y 400 -ksp_type gmres -pc_type mg -log_view | grep "Time (sec):"
Time (sec):           3.066e+01      1.00000   3.066e+01
-bash-4.2$ mpiexec -n 18 ./ex19 -da_grid_x 200 -da_grid_y 200 -ksp_type gmres -pc_type mg -log_view | grep "Time (sec):"
Time (sec):           1.724e+00      1.00003   1.724e+00


baseline tests for ex48:

GMRES AND BJACOBI
-bash-4.2$ mpiexec -n 18 ./ex48 -da_grid_x 200 -da_grid_y 200 -ksp_type gmres -pc_type bjacobi -log_view | grep "Time (sec):"
Time (sec):           3.298e+01      1.00000   3.298e+01
-bash-4.2$ mpiexec -n 18 ./ex48 -da_grid_x 100 -da_grid_y 100 -ksp_type gmres -pc_type bjacobi -log_view | grep "Time (sec):"
Time (sec):           1.782e+00      1.00004   1.781e+00

CG AND MG
bassh-4.2$ mpiexec -n 18 ./ex48 -da_grid_x 100 -da_grid_y 100 -ksp_type cg -pc_type mg -log_view | grep "Time (sec):"
Time (sec):           1.650e+00      1.00003   1.650e+00
-bash-4.2$ mpiexec -n 18 ./ex48 -da_grid_x 200 -da_grid_y 200 -ksp_type cg -pc_type mg -log_view | grep "Time (sec):"
Time (sec):           3.022e+01      1.00001   3.022e+01




5 Min Prediction for each type:

EX48
for cg and mg predict to take 5-10 min
-bash-4.2$ mpiexec -n 18 ./ex48 -da_grid_x 400 -da_grid_y 400 -ksp_type cg -pc_type mg -log_view | grep "Time (sec):"
Time (sec):           2.524e+02      1.00000   2.524e+02

The program ran slightly faster than the prediction time and spent most of its time doing Matrix multiplication.
Prediction for gmres and bjacobi. since the two had similar run times with gmres/bjacobi being slightly slower, I will predict that making the grid 450 by 450 will result in approximately a 7 min run tmie.
First guess
-bash-4.2$ mpiexec -n 18 ./ex48 -da_grid_x 400 -da_grid_y 400 -ksp_type cg -pc_type bjacobi -log_view | grep "Time (sec):"
Time (sec):           1.524e+02      1.00000   1.524e+02

A second guess
-bash-4.2$ mpiexec -n 18 ./ex48 -da_grid_x 500 -da_grid_y 500 -ksp_type cg -pc_type bjacobi -log_view | grep "Time (sec):"
Time (sec):           2.997e+02      1.00000   2.997e+02
As we can see, cg and block jacobi performs better than 



mpiexec -n 18 ./ex48 -da_grid_x 350 -da_grid_y 350 -ksp_type gmres -pc_type bjacobi -log_view | grep "Time (sec):"
Failed to converge with these dimensions


mpiexec -n 18 ./ex19 -da_grid_x 900 -da_grid_y 900 -ksp_type gmres -pc_type mg -log_view | grep "Time (sec):"
Time (sec):           3.059e+02      1.00000   3.059e+02
My first guess was extremely low(I tested 500x500) and then changed my guess to 900 and it was right around 6 min.
This spent most of its time on vecmdot and MatSOR.


Next guess using gmres/bjacobi was way off
-bash-4.2$ mpiexec -n 18 ./ex19 -da_grid_x 800 -da_grid_y 800 -ksp_type gmres -pc_type bjacobi -log_view | grep "Time (sec):"
Time (sec):           1.665e+02      1.00000   1.665e+02
Time seemed to be equally spent between VecMDot and MatMult

-bash-4.2$ mpiexec -n 18 ./ex19 -da_grid_x 1000 -da_grid_y 1000 -ksp_type gmres -pc_type bjacobi -log_view | grep "Time (sec):"
Time (sec):           3.720e+02      1.00000   3.720e+02



gmres bjacobi runs the fastest for ex19 



