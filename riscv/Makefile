# Folder Path
src = src
testspace = testspace

sim_testcase = testcase/sim
fpga_testcase = testcase/fpga

sim = sim
riscv_toolchain = /opt/riscv
riscv_bin = $(riscv_toolchain)/bin
sys = sys

fpga = fpga

# All build result are put at testspace
build_sim:
	iverilog -o $(testspace)/bench $(sim)/testbench.v $(src)/common/block_ram/*.v $(src)/common/fifo/*.v $(src)/common/uart/*.v $(src)/*.vh $(src)/*.v

build_sim_test:
	$(riscv_bin)/riscv32-unknown-elf-as -o $(sys)/rom.o -march=rv32i $(sys)/rom.s
	cp $(sim_testcase)/*$(name)*.c $(testspace)/test.c
	$(riscv_bin)/riscv32-unknown-elf-gcc -o $(testspace)/test.o -I $(sys) -c $(testspace)/test.c -O2 -march=rv32i -mabi=ilp32 -Wall
	$(riscv_bin)/riscv32-unknown-elf-ld -T $(sys)/memory.ld $(sys)/rom.o $(testspace)/test.o -L $(riscv_toolchain)/riscv32-unknown-elf/lib/ -L $(riscv_toolchain)/lib/gcc/riscv32-unknown-elf/8.2.0/ -lc -lgcc -lm -lnosys -o $(testspace)/test.om
	$(riscv_bin)/riscv32-unknown-elf-objcopy -O verilog $(testspace)/test.om $(testspace)/test.data
	$(riscv_bin)/riscv32-unknown-elf-objdump -D $(testspace)/test.om > $(testspace)/test.dump

run_sim:
	cd $(testspace) && ./bench

clear:
	rm $(sys)/rom.o $(testspace)/test.*

test_sim: build_sim build_sim_test run_sim

.PHONY: build_sim build_sim_test run_sim clear test_sim