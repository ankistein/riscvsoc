PROJECT=fpga_riscvsoc
PART=xc6slx9-ftg256-3

EXTRA_EFFORT=n

all: ${PROJECT}_routed.bit

project/${PROJECT}.prj:
	mkdir -p project
	echo "" > project/${PROJECT}.prj
#	echo "verilog work \"../rs232rx.v\"" >> project/${PROJECT}.prj 
#	echo "verilog work \"../rs232tx.v\"" >> project/${PROJECT}.prj 
#	echo "verilog work \"../rs232.v\"" >> project/${PROJECT}.prj 
	echo "verilog work \"../uart.v\"" >> project/${PROJECT}.prj 
	echo "verilog work \"../yarvi.v\"" >> project/${PROJECT}.prj 
#	echo "verilog work \"../uart_controller.v\"" >> project/${PROJECT}.prj 
#	echo "verilog work \"../gpio_controller.v\"" >> project/${PROJECT}.prj 
	echo "verilog work \"../ram_wb.v\"" >> project/${PROJECT}.prj 
	echo "verilog work \"../uart_wb.v\"" >> project/${PROJECT}.prj 
	echo "verilog work \"../riscvsoc.v\"" >> project/${PROJECT}.prj 
	echo "verilog work \"../clk_100.v\"" >> project/${PROJECT}.prj 
#	echo "verilog work \"../clk_200.v\"" >> project/${PROJECT}.prj 
	echo "verilog work \"../top_riscvsoc.v\"" >> project/${PROJECT}.prj 

#	echo "verilog work \"../top.v\"" > project/${PROJECT}.prj
#	echo "verilog work \"../cores/ftdi_async_bridge/rtl/ftdi_if.v\"" >> project/${PROJECT}.prj
#	echo "verilog work \"../cores/ftdi_async_bridge/rtl/ftdi_sync.v\"" >> project/${PROJECT}.prj
#	echo "verilog work \"../cores/sdram/rtl/sdram.v\"" >> project/${PROJECT}.prj
#	echo "vhdl work \"../clk_50_100.vhd\"" >> project/${PROJECT}.prj

${PROJECT}.ngc: project/${PROJECT}.prj	
	cp ${PROJECT}.* project/
	mkdir -p project/xst/projnav.tmp/
	cd project; xst -intstyle ise -ifn ${PROJECT}.xst -ofn ${PROJECT}.syr
        
${PROJECT}.ngd: ${PROJECT}.ngc
	cd project; ngdbuild -intstyle ise -dd _ngo -nt timestamp \
	-uc ${PROJECT}.ucf -p ${PART} ${PROJECT}.ngc ${PROJECT}.ngd

${PROJECT}.ncd: ${PROJECT}.ngd
	cd project; map -convert_bram8 -w -intstyle ise -p ${PART} \
	-detail -ir off -ignore_keep_hierarchy -pr b -timing -ol high -xe ${EXTRA_EFFORT} -logic_opt on  \
	-o ${PROJECT}.ncd ${PROJECT}.ngd ${PROJECT}.pcf 

${PROJECT}_routed.ncd: ${PROJECT}.ncd
	cd project; par -w -intstyle ise -ol high -xe ${EXTRA_EFFORT} ${PROJECT}.ncd ${PROJECT}_routed.ncd ${PROJECT}.pcf

${PROJECT}_routed.bit: ${PROJECT}_routed.ncd
	cd project; bitgen -f ${PROJECT}.ut ${PROJECT}_routed.ncd

timing_report:
	cd project; trce -v 12 -fastpaths -o design_timing_report ${PROJECT}_routed.ncd ${PROJECT}.pcf

clean:
	@rm -rf project
#-DSIMULATION 
sim:
	iverilog -o /tmp/riscv uart.v yarvi.v ram_wb.v uart_wb.v gpio_controller.v riscvsoc.v test_riscvsoc.v
	/tmp/riscv