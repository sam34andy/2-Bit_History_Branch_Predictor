# 2-Bit_History_branch_Predictor
2019 Data Structure Course Homework One

### 題目: 2-Bit History Branch Prediction 
### 要求:
	1.讀取一個序列的PC與instruction\n
	2.可以設定predictor的entry數量

### 使用語言: C++
### 範例input(using txt file "ASSEMBLY.txt"):

	0x110	li R2,0
	0x114	li R3,4
	0x118	li R4,0
	LoopI:
	0x11C	beq R4,R3,EndLoopI
	0x120	li R5,0
	LoopJ:
	0x124	beq R5,R3,EndLoopJ
	0x128	add R6,R5,R4
	0x12C	addi R6,R6,1
	0x130	bne R6,R0,Endif
	0x134	add R2,R2,R5
	Endif:
	0x138	addi R5,R5,1
	0x13C	beq R0,R0,LoopJ
	EndLoopJ:
	0x140	addi R4,R4,1
	0x144	beq R0,R0,LoopI
	EndLoopI:

### 範例output:
