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
	
### 資料儲存形態與內容:
	Inst_history:將所有instruction以string的方式做儲存。一條instruction占vector一個[i]的位置。
	Tbh:一個entry占vector一個[i]的位置。包含所有2-bit history predictor。
	Reg:儲存Register的資料。一條Register占vector一個[i]的位置。
	Inst:每條instruction存在程式碼中的樣子，包含著對應的資料型態。一條instruction占vector一個[i]的位置。
	jump_addr:儲存所有可能branch或是jump到的標籤位置。一條標籤占vector一個[i]的位置。
	
### 程式碼呼叫順序與函式執行內容:
	main  // input predictor的entry數量
		-> string_cut // 將每行instruction中的資料，一個個有意義的文字切割出來
			-> Data_Saving // 將string_cut中切割好的資料丟到Data_Saving中進行對應資料型態的儲存。
		-> Calculating_Assembly
			-> (指令為加法或減法)TBH_predict
			-> (指令為branch或jump)Find_matching_addr(找到要跳過去的對應instruction內容) + TBH_predict
			
	TBH_predict執行內容(按順序):
		1.根據history做出predict
		2.根據predict與輸入的outcome判斷有沒有預測正確
		3.更新History的值
		4.根據outcome改變我們的Two Bit History
		5.權重修正(*)
		6.將更新的predictor內容以string方式進行儲存，方便後續的輸出。
		
	*權重:
		SN, WN, WT, ST分別對應權重0, 1, 2, 3。如果outcome為True，則會對entry相對應的位置的紀錄進行權重+1。如果outcome為False，則會對	entry相對應的位置的紀錄進行權重-1。
		
