#RUN=srun -n 7 ../src/dlars
RUN=../src/dlars
COMPARE=perl ../../compare/compare.pl
all: lars lasso stopping split weights restart restart2 con_grad distribute restart_mpi restart_mpi_nodist restart3

lars:
	$(RUN) Xcpp.txt Ycpp.txt Xcpp.dim --algorithm=lars --normalize=y > dlars.cpp.txt
	-$(COMPARE) dlars.cpp.txt correct_output/dlars.cpp.txt
	$(RUN) X.64.32.txt Y.64.32.txt X.64.32.dim --algorithm=lars --normalize=y > dlars.64.32.txt
	-$(COMPARE) dlars.64.32.txt correct_output/dlars.64.32.txt
	$(RUN) A.txt b.txt A.dim --algorithm=lars --normalize=y > dlars.A.txt
	-$(COMPARE) dlars.A.txt correct_output/dlars.A.txt

lasso:
	$(RUN) Xcpp.txt Ycpp.txt Xcpp.dim --normalize=y > dlasso.cpp.txt
	-$(COMPARE) dlasso.cpp.txt correct_output/dlasso.cpp.txt
	$(RUN) X.64.32.txt Y.64.32.txt X.64.32.dim --normalize=y > dlasso.64.32.txt
	-$(COMPARE) dlasso.64.32.txt correct_output/dlasso.64.32.txt
	$(RUN) A.txt b.txt A.dim --normalize=n > dlasso.A.nonorm.txt
	-$(COMPARE) dlasso.A.nonorm.txt correct_output/dlasso.A.nonorm.txt
	$(RUN) A.txt b.txt A.dim --normalize=y > dlasso.A.txt
	-$(COMPARE) dlasso.A.txt correct_output/dlasso.A.txt

stopping:
	$(RUN) X.64.32.txt Y.64.32.txt X.64.32.dim --max_norm=20 --normalize=y > dlasso.64.32.mn.txt
	-$(COMPARE) dlasso.64.32.mn.txt correct_output/dlasso.64.32.mn.txt
	$(RUN) X.64.32.txt Y.64.32.txt X.64.32.dim --lambda=5.0e-03 --normalize=y > dlasso.64.32.lam.txt
	-$(COMPARE) dlasso.64.32.lam.txt correct_output/dlasso.64.32.lam.txt
	$(RUN) X.64.32.txt Y.64.32.txt X.64.32.dim --iterations=10 --normalize=y > dlasso.64.32.its.txt
	-$(COMPARE) dlasso.64.32.its.txt correct_output/dlasso.64.32.its.txt

split:
	srun -n 2 ../src/dlars A.txt b.txt dim.txt --split_files --normalize=y >& dlasso.A.split.txt
	-$(COMPARE) dlasso.A.split.txt correct_output/dlasso.A.split.txt
	srun -n 4 ../src/dlars A.txt b.txt dim.txt --split_files --normalize=y >& dlasso.A.split.4.txt
	-$(COMPARE) dlasso.A.split.4.txt correct_output/dlasso.A.split.4.txt
	srun -n 7 ../src/dlars A.txt b.txt dim.txt --split_files --normalize=y >& dlasso.A.split.7.txt
	-$(COMPARE) dlasso.A.split.7.txt correct_output/dlasso.A.split.7.txt

distribute:
	srun -n 2 ../src/dlars A.txt b.txt dim.txt --split_files --distributed_solver=y --normalize=y >& dlasso.A.dist.txt
	-$(COMPARE) dlasso.A.dist.txt correct_output/dlasso.A.dist.txt
	srun -n 4 ../src/dlars A.txt b.txt dim.txt --split_files --distributed_solver=y --normalize=y >& dlasso.A.dist.4.txt
	-$(COMPARE) dlasso.A.dist.4.txt correct_output/dlasso.A.dist.4.txt
	srun -n 7 ../src/dlars A.txt b.txt dim.txt --split_files --distributed_solver=y --normalize=y >& dlasso.A.dist.7.txt
	-$(COMPARE) dlasso.A.dist.7.txt correct_output/dlasso.A.dist.7.txt

weights:
	$(RUN) Xcpp.txt Ycpp.txt Xcpp.dim --weights=Ycpp.weights --normalize=y > dlasso.weights.txt
	-$(COMPARE) dlasso.weights.txt correct_output/dlasso.weights.txt
	$(RUN) Xcpp.txt Ycpp.txt Xcpp.dim --feature_weights=feature.weights --normalize=y > dlasso.feature.txt
	-$(COMPARE) dlasso.feature.txt correct_output/dlasso.feature.txt

restart:
	$(RUN) A.txt b.txt A.dim --iterations=10 --normalize=y > dlasso.run1.txt
	cp restart.txt restart.save
	$(RUN) A.txt b.txt A.dim --restart=restart.save --normalize=y > dlasso.run2.txt
	-$(COMPARE) dlasso.run2.txt correct_output/dlasso.run2.txt

restart_mpi:
	srun -n 2 ../src/dlars A.txt b.txt dim.txt --split_files --distributed_solver=y --iterations=10 --normalize=y > dlasso.restart_mpi.txt
	cp restart.0000.txt restart.save.0000.txt
	cp restart.0001.txt restart.save.0001.txt
	srun -n 2 ../src/dlars A.txt b.txt dim.txt --split_files --restart=restart.save --distributed_solver=y --normalize=y > dlasso.restart_mpi2.txt
	-$(COMPARE) dlasso.restart_mpi2.txt correct_output/dlasso.restart_mpi2.txt

restart_mpi_nodist:
	srun -n 2 ../src/dlars A.txt b.txt A.dim --iterations=10 --normalize=y > dlasso.restart.nodist1.txt
	cp restart.0000.txt restart.nodist.0000.txt
	cp restart.0001.txt restart.nodist.0001.txt
	srun -n 2 ../src/dlars A.txt b.txt A.dim --restart=restart.nodist --normalize=y > dlasso.restart.nodist2.txt
	-$(COMPARE) dlasso.restart.nodist2.txt correct_output/dlasso.restart.nodist2.txt

restart_mpi_congrad:
	srun -n 2 ../src/dlars A.txt b.txt A.dim --iterations=10 --normalize=y --con_grad --precondition > dlasso.restart.congrad1.txt
	cp restart.0000.txt restart.congrad.0000.txt
	cp restart.0001.txt restart.congrad.0001.txt
	srun -n 2 ../src/dlars A.txt b.txt A.dim --restart=restart.congrad --normalize=y --con_grad --precondition > dlasso.restart.congrad2.txt
	-$(COMPARE) dlasso.restart.congrad2.txt correct_output/dlasso.restart.congrad2.txt

restart2:
	$(RUN) A.txt b.txt A.dim --iterations=20 --normalize=y > dlasso.restart2.1.txt
	cp restart.txt restart.save
	$(RUN) A.txt b.txt A.dim --restart=restart.save --normalize=y > dlasso.restart2.2.txt
	-$(COMPARE) dlasso.restart2.2.txt correct_output/dlasso.restart2.2.txt

restart3:
	$(RUN) A.txt b.txt A.dim --iterations=20 --con_grad --precondition --normalize=y > dlasso.restart3.1.txt
	cp restart.txt restart.save
	$(RUN) A.txt b.txt A.dim --restart=restart.save --con_grad --precondition --normalize=y > dlasso.restart3.2.txt
	-$(COMPARE) dlasso.restart3.2.txt correct_output/dlasso.restart3.2.txt

con_grad:
	$(RUN) Xcpp.txt Ycpp.txt Xcpp.dim --con_grad --weights=Ycpp.weights --normalize=y > dlasso.con_grad.txt
	-$(COMPARE) dlasso.con_grad.txt correct_output/dlasso.con_grad.txt
	$(RUN) A.txt b.txt A.dim --con_grad --normalize=y > dlasso.con_grad.A.txt
	-$(COMPARE) dlasso.con_grad.A.txt correct_output/dlasso.con_grad.A.txt
	$(RUN) A.txt b.txt A.dim --con_grad --precondition --normalize=y > dlasso.precon.A.txt
	-$(COMPARE) dlasso.precon.A.txt correct_output/dlasso.precon.A.txt

clean:
	rm -f dlars.*.txt dlasso.*.txt *.diff

