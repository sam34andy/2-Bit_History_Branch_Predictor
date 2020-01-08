# 2-Bit_History_branch_Predictor
2019 Data Structure Course Homework One

### 題目: 2-Bit History Branch Prediction 

### 要求:
	1.讀取一個序列的PC與instruction
	2.可以設定predictor的entry數量

### 使用語言: C++

### 編譯方式:
	1.使用Dev-C++、Visual Studio等程式完成編譯
	2.linux "g++ hw1_upgrade.cpp ASSEMBLY.txt -o hw1"
	
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
	1.包含全部entry跑完全整份input file data的所有prediction結果。
	(因為長度問題，截圖並未包含全部的prediction結果)
	P:Prediction
	O:Outcome
	G:Guess
![image](https://github.com/sam34andy/2-Bit_History_Branch_Predictor/blob/master/hw1_example_output1.JPG)
	
	2.包含個別entry他們執行prediction的結果與最終這個entry所遇上的misprediction次數。
![image](https://github.com/sam34andy/2-Bit_History_Branch_Predictor/blob/master/hw1_example_output2.JPG)
	
### 程式碼內容:
	struct TBH {
		int history[2];
		int CNT[4];
		int mis_pre;
		vector<string> h; // hsitory;
	}; // TBH is Two Bit History. // SN-0, WN-1, WT-2, ST-3

	struct Register { 
		int counter;
		int value;
	}; // Register

	struct Instruction{
		string addr;
		string Type;
		int D_Reg;
		int InputA_Reg;
		int InputB_Reg;
		int immed; // immediate number
		string D_Addr;
	}; // instruction存在程式碼中的樣子

	struct Jump_Addr {
		string addr;
		int inst_number;
	}; // 要jump/branch的位置與他們的PC

	vector<string>Inst_history;
	vector<TBH>Tbh;
	vector<int>Reg;
	vector<Instruction>Inst;
	vector<Jump_Addr>jump_addr;

	void string_cut(string, int); // 進行字串切割
	void Data_Saving(string, int, int); // 進行字符判別
	void Calculating_Assembly(int); // 對instruction做計算
	int Find_matching_addr(string); // Branch發生時跳到指定的位置
	void TBH_predict(int, int, char); // 2-Bit History branch Predoction主要執行區

