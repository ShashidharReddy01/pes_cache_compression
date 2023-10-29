# CACHE COMPRESSION

**CACHE COMPRESSION: BASE-DELTA-IMMEDIATE ALGO**
<br/>
<br/>
![img](https://askleo.askleomedia.com/wp-content/uploads/2013/11/cache.jpg)
<br/>

![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/b4ce88f5-53f2-4b11-a3f3-832c2ee18c89)

![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/3689c477-553f-4155-aa65-ae04b27a713d)

<br/>

# *Implementation of BDI cache line compression algorithm*

<details>
<summary>Project Overview</summary>
  
### **Project Overview:**

**1. Cache Memory:** Cache memory is a crucial component in modern CPUs. It provides a small, high-speed memory that stores frequently accessed data, reducing the time required to access data from slower main memory. Cache memory typically consists of multiple levels, such as L1, L2, and L3 caches, with each level having different characteristics.

**2. Cache Compression:** Cache compression techniques are employed to maximize the effective use of limited on-chip cache resources. Compressed cache entries occupy less space, which can lead to higher cache hit rates and better overall system performance.

**3. BDI (Base Delta Immediate) Algorithm:** BDI is a simple yet efficient compression algorithm designed for use in cache memory. It focuses on compressing common in-cache data patterns while minimizing the impact on cache access latency. The BDI algorithm often employs delta encoding, where the difference between consecutive values is stored instead of the actual values.
</details>
<details>
<summary>Project Goals and Objectives</summary>
  
### **Project Goals and Objectives:**

- **Increase Cache Capacity:** The primary goal of this project is to increase the effective capacity of the cache by compressing data before storing it. This can potentially reduce cache misses and improve CPU performance.

- **Low Latency:** To maintain high-speed cache access, the BDI algorithm is chosen for its simplicity and minimal decompression/compression latency. It ensures that the time required to access data from the cache is not negatively affected.

- **Verilog Implementation:** The project is implemented in Verilog, a hardware description language commonly used for digital circuit design. Verilog allows for the description of hardware components and their interactions.
</details>
<details>
<summary>Key Components of the Project</summary>
  
### **Key Components of the Project:**

- **BDI Compression Module:** This module is responsible for implementing the BDI compression algorithm. It takes cache data as input and produces compressed data for storage in the cache.

- **BDI Decompression Module:** This module is responsible for decompressing data when it is read from the cache. It reverses the compression process to provide the original data to the CPU.

- **Cache Controller:** The cache controller manages the flow of data in and out of the cache. It interacts with the compression and decompression modules to ensure correct data storage and retrieval.

- **Testing and Verification:** Rigorous testing and verification are essential to ensure that the compression and decompression processes work correctly and do not introduce errors or latency.
</details>
<details>
<summary>Project Benefits</summary>
  
### **Project Benefits:**


- Improved Cache Performance: Compressed cache entries occupy less space, allowing for more data to be stored in the cache, potentially reducing cache misses and improving overall CPU performance.

- Efficient Use of Resources: By choosing the BDI algorithm for compression, the project aims to strike a balance between compression efficiency and low latency, ensuring that cache access remains fast.

- Hardware Implementation: The use of Verilog enables the project to be implemented directly in hardware, making it suitable for integration into various CPU architectures.
</details>

<details>
<summary>Verilog Code</summary>
  
```
  module pes_cache_compression(clock,UnCompressedCache,CompressedCache,DeCompressedCache);
input clock;
input [255:0]UnCompressedCache;
output reg[255:0]CompressedCache,DeCompressedCache;
reg mark8_1=0,mark8_2=0,mark8_3=0;
reg mark4_1 = 0, mark4_2 = 0,mark4_3 = 0,mark4_4 = 0,mark4_5 = 0,mark4_6 = 0,mark4_7 = 0;
reg mark2_1 = 0, mark2_2 = 0,mark2_3 = 0,mark2_4 = 0,mark2_5 = 0,mark2_6 = 0,mark2_7 = 0,mark2_8 = 0,
    mark2_9 = 0,mark2_10 = 0,mark2_11 = 0,mark2_12 = 0,mark2_13 = 0,mark2_14 = 0,mark2_15 = 0;

reg [95:0]CCL1,CCL4;
reg [127:0]CCL2;
reg [191:0]CCL3;
reg [159:0]CCL5;
reg [143:0]CCL6;

reg CoN1,CoN2,CoN3,CoN4,CoN5,CoN6,CoN7,CoN8;
reg [63:0]Base8;
reg [63:0]del8_1,del8_2,del8_3;
reg [31:0]Base4;
reg [31:0] del4_1,del4_2,del4_3,del4_4,del4_5,del4_6,del4_7;
reg [15:0]Base2;
reg [15:0] del2_1,del2_2,del2_3,del2_4,del2_5,del2_6,del2_7,del2_8,del2_9,del2_10,del2_11,del2_12,del2_13,del2_14,del2_15;




//COMPRESSOR BLOCK

always @ (posedge clock)
begin

Base8 = UnCompressedCache[63:0]; //Base is the first value
Base4 = UnCompressedCache[31:0];
Base2 = UnCompressedCache[15:0];

$display("input = %h\n",UnCompressedCache);

//BASE 8

//Calculating all the deltas
if (Base8 > UnCompressedCache[127:64])
del8_1 = Base8 - UnCompressedCache[127:64];
else 
begin
del8_1 = UnCompressedCache[127:64] - Base8 ;
mark8_1 =1;
end

if (Base8 > UnCompressedCache[191:128])
del8_2 = Base8 - UnCompressedCache[191:128];
else 
begin
del8_2 = UnCompressedCache[191:128] - Base8 ;
mark8_2 = 1;
end

if (Base8 > UnCompressedCache[255:192])
del8_3 = Base8 - UnCompressedCache[255:192];
else 
begin
del8_3 = UnCompressedCache[255:192] - Base8 ;
mark8_3=1;
end
$display (" 8_1: del1= %h, del2 =%h, del3 = %h \n",del8_1,del8_2,del8_3);


// Delta = 1 byte
if( ((del8_1[63:8]==56'hFFFFFFFFFFFFFF) || (del8_1[63:8]==56'h00000000000000)) && ((del8_2[63:8]==56'hFFFFFFFFFFFFFF) || (del8_2[63:8]==56'h00000000000000))
		&& ((del8_3[63:8]==56'hFFFFFFFFFFFFFF) || (del8_3[63:8]==56'h00000000000000)))
begin
CoN1=1;
CCL1 = {del8_3[7:0],del8_2[7:0],del8_1[7:0],8'd0,Base8};
end

else 
begin
CoN1 =0;
end
$display ("CoN1 = %b, CCL1 = %h ", CoN1,CCL1);

// Delta = 2 bytes
if( ((del8_1[63:16]==48'hFFFFFFFFFFFF) || (del8_1[63:16]==48'h000000000000)) && ((del8_2[63:16]==48'hFFFFFFFFFFFF) || (del8_2[63:16]==48'h000000000000))
		&& ((del8_3[63:16]==48'hFFFFFFFFFFFF) || (del8_3[63:16]==48'h000000000000)))
begin
CoN2=1;
CCL2 = {del8_3[15:0],del8_2[15:0],del8_1[15:0],16'd0,Base8};
end

else 
begin
CoN2 =0;
end
$display ("CoN2 = %b, CCL2 = %h ", CoN2,CCL2);

// Delta = 4 bytes
if( ((del8_1[63:32]==32'hFFFFFFFF) || (del8_1[63:32]==32'h00000000)) && ((del8_2[63:32]==32'hFFFFFFFF) || (del8_2[63:32]==32'h00000000))
		&& ((del8_3[63:32]==32'hFFFFFFFF) || (del8_3[63:32]==32'h00000000)))
begin
CoN3=1;
CCL3 = {del8_3[31:0],del8_2[31:0],del8_1[31:0],32'd0,Base8};
end

else 
begin
CoN3 =0;
end
$display ("CoN3 = %b, CCL3 = %h ", CoN3,CCL3);



//BASE 4

//Calculating all the deltas

if (Base4 > UnCompressedCache[63:32])
del4_1 = Base4 - UnCompressedCache[63:32];
else 
begin
del4_1 = UnCompressedCache[63:32] - Base4 ;
mark4_1 =1;
end

if (Base4 > UnCompressedCache[95:64])
del4_2 = Base4 - UnCompressedCache[95:64];
else 
begin
del4_2 = UnCompressedCache[95:64] - Base4 ;
mark4_2 =1;
end

if (Base4 > UnCompressedCache[127:96])
del4_3 = Base4 - UnCompressedCache[127:96];
else 
begin
del4_3 = UnCompressedCache[127:96] - Base4 ;
mark4_3 =1;
end

if (Base4 > UnCompressedCache[159:128])
del4_4 = Base4 - UnCompressedCache[159:128];
else 
begin
del4_4 = UnCompressedCache[159:128] - Base4 ;
mark4_4 =1;
end

if (Base4 > UnCompressedCache[191:160])
del4_5 = Base4 - UnCompressedCache[191:160];
else 
begin
del4_5 = UnCompressedCache[191:160] - Base4 ;
mark4_5 =1;
end

if (Base4 > UnCompressedCache[223:192])
del4_6 = Base4 - UnCompressedCache[223:192];
else 
begin
del4_6 = UnCompressedCache[223:192] - Base4 ;
mark4_6 =1;
end

if (Base4 > UnCompressedCache[255:224])
del4_7 = Base4 - UnCompressedCache[255:224];
else 
begin
del4_7 = UnCompressedCache[255:224] - Base4 ;
mark4_7 =1;
end


$display (" BASE 4: del1= %h, del2 =%h, del3 = %h del4 = %h del5 = %h del6 = %h del7 = %h\n",del4_1,del4_2,del4_3,del4_4,del4_5,del4_6,del4_7);


// DELTA = 1 BYTE 
if( ((del4_1[31:8]==24'hFFFFFF) || (del4_1[31:8]==24'h000000)) && ((del4_2[31:8]==24'hFFFFFF) || (del4_2[31:8]==24'h000000))
		&& ((del4_3[31:8]==24'hFFFFFF) || (del4_3[31:8]==24'h000000))  && ((del4_4[31:8]==24'hFFFFFF) || (del4_4[31:8]==24'h000000))  
		&& ((del4_5[31:8]==24'hFFFFFF) || (del4_5[31:8]==24'h000000))  && ((del4_6[31:8]==24'hFFFFFF) || (del4_6[31:8]==24'h000000))
		&& ((del4_7[31:8]==24'hFFFFFF) || (del4_7[31:8]==24'h000000)))
begin
CoN4=1;
CCL4 = {del4_7[7:0],del4_6[7:0],del4_5[7:0],del4_4[7:0],del4_3[7:0],del4_2[7:0],del4_1[7:0],8'd0,Base4};
end

else 
begin
CoN4 =0;
end
$display ("CoN4 = %b, CCL4 = %h ", CoN4,CCL4);


// DELTA = 2 BYTES
if( ((del4_1[31:16]==16'hFFFF) || (del4_1[31:16]==16'h0000)) && ((del4_2[31:16]==16'hFFFF) || (del4_2[31:16]==16'h0000))
		&& ((del4_3[31:16]==16'hFFFF) || (del4_3[31:16]==16'h0000))  && ((del4_4[31:16]==16'hFFFF) || (del4_4[31:16]==16'h0000))  
		&& ((del4_5[31:16]==16'hFFFF) || (del4_5[31:16]==16'h0000))  && ((del4_6[31:16]==16'hFFFF) || (del4_6[31:16]==16'h0000))
		&& ((del4_7[31:16]==16'hFFFF) || (del4_7[31:16]==16'h0000)))
begin
CoN5=1;
CCL5 = {del4_7[15:0],del4_6[15:0],del4_5[15:0],del4_4[15:0],del4_3[15:0],del4_2[15:0],del4_1[15:0],16'd0,Base4};
end

else 
begin
CoN5 =0;
end
$display ("CoN5 = %b, CCL5 = %h ", CoN5,CCL5);



//BASE 2

//Calculating all the deltas

if (Base2 > UnCompressedCache[31:16])
del2_1 = Base2 - UnCompressedCache[31:16];
else 
begin
del2_1 = UnCompressedCache[31:16] - Base2 ;
mark2_1 =1;
end

if (Base2 > UnCompressedCache[47:32])
del2_2 = Base2 - UnCompressedCache[47:32];
else 
begin
del2_2 = UnCompressedCache[47:32] - Base2 ;
mark2_2 =1;
end

if (Base2 > UnCompressedCache[63:48])
del2_3 = Base2 - UnCompressedCache[63:48];
else 
begin
del2_3 = UnCompressedCache[63:48] - Base2 ;
mark2_3 =1;
end

if (Base2 > UnCompressedCache[79:64])
del2_4 = Base2 - UnCompressedCache[79:64];
else 
begin
del2_4 = UnCompressedCache[79:64] - Base2 ;
mark2_4 =1;
end

if (Base2 > UnCompressedCache[95:80])
del2_5 = Base2 - UnCompressedCache[95:80];
else 
begin
del2_5 = UnCompressedCache[95:80] - Base2 ;
mark2_5 =1;
end

if (Base2 > UnCompressedCache[111:96])
del2_6 = Base2 - UnCompressedCache[111:96];
else 
begin
del2_6 = UnCompressedCache[111:96] - Base2 ;
mark2_6 =1;
end

if (Base2 > UnCompressedCache[127:112])
del2_6 = Base2 - UnCompressedCache[127:112];
else 
begin
del2_6 = UnCompressedCache[127:112] - Base2 ;
mark2_6 =1;
end

if (Base2 > UnCompressedCache[143:128])
del2_7 = Base2 - UnCompressedCache[143:128];
else 
begin
del2_7 = UnCompressedCache[143:128] - Base2 ;
mark2_7 =1;
end

if (Base2 > UnCompressedCache[159:144])
del2_8 = Base2 - UnCompressedCache[159:144];
else 
begin
del2_8 = UnCompressedCache[159:144] - Base2 ;
mark2_8 =1;
end

if (Base2 > UnCompressedCache[175:160])
del2_9 = Base2 - UnCompressedCache[175:160];
else 
begin
del2_9 = UnCompressedCache[175:160] - Base2 ;
mark2_9 =1;
end

if (Base2 > UnCompressedCache[191:176])
del2_10 = Base2 - UnCompressedCache[191:176];
else 
begin
del2_10 = UnCompressedCache[191:176] - Base2 ;
mark2_10 =1;
end

if (Base2 > UnCompressedCache[191:176])
del2_11 = Base2 - UnCompressedCache[191:176];
else 
begin
del2_11 = UnCompressedCache[191:176] - Base2 ;
mark2_11 =1;
end

if (Base2 > UnCompressedCache[207:192])
del2_12 = Base2 - UnCompressedCache[207:192];
else 
begin
del2_12 = UnCompressedCache[207:192] - Base2 ;
mark2_12 =1;
end

if (Base2 > UnCompressedCache[223:208])
del2_13 = Base2 - UnCompressedCache[223:208];
else 
begin
del2_13 = UnCompressedCache[223:208] - Base2 ;
mark2_13 =1;
end

if (Base2 > UnCompressedCache[239:224])
del2_14 = Base2 - UnCompressedCache[239:224];
else 
begin
del2_14 = UnCompressedCache[239:224] - Base2 ;
mark2_14 =1;
end

if (Base2 > UnCompressedCache[255:240])
del2_15 = Base2 - UnCompressedCache[255:240];
else 
begin
del2_15 = UnCompressedCache[255:240] - Base2 ;
mark2_15 =1;
end

$display (" BASE 2: del1= %h, del2 =%h, del3 = %h del4 = %h del5 = %h del6 = %h del7 = %h	del8= %h, del9 =%h, del10 = %h del11 = %h del12 = %h del13 = %h del14 = %h del7 = %h \n"
				,del2_1,del2_2,del2_3,del2_4,del2_5,del2_6,del2_7,del2_8,del2_9,del2_10,del2_11,del2_12,del2_13,del2_14,del2_15);

// DELTA = 1 BYTE 
if( ((del2_1[15:8]==24'hFF) || (del2_1[15:8]==24'h00)) && ((del2_2[15:8]==24'hFF) || (del2_2[15:8]==24'h00))
		&& ((del2_3[15:8]==24'hFF) || (del2_3[15:8]==24'h00))  && ((del2_4[15:8]==24'hFF) || (del2_4[15:8]==24'h00))  
		&& ((del2_5[15:8]==24'hFF) || (del2_5[15:8]==24'h00))  && ((del2_6[15:8]==24'hFF) || (del2_6[15:8]==24'h00))
		&& ((del2_7[15:8]==24'hFF) || (del2_7[15:8]==24'h00))  && ((del2_8[15:8]==24'hFF) || (del2_8[15:8]==24'h00))
		&& ((del2_9[15:8]==24'hFF) || (del2_9[15:8]==24'h00))  && ((del2_10[15:8]==24'hFF) || (del2_10[15:8]==24'h00))
		&& ((del2_11[15:8]==24'hFF)|| (del2_11[15:8]==24'h00))&& ((del2_12[15:8]==24'hFF) || (del2_12[15:8]==24'h00))
		&& ((del2_13[15:8]==24'hFF)|| (del2_13[15:8]==24'h00))&& ((del2_14[15:8]==24'hFF) || (del2_14[15:8]==24'h00))
		&& ((del2_15[15:8]==24'hFF)|| (del2_15[15:8]==24'h00)))
begin
CoN6=1;
CCL6 = {del2_15[7:0],del2_14[7:0],del2_13[7:0],del2_12[7:0],del2_11[7:0],del2_10[7:0],del2_9[7:0],del2_8[7:0],del2_7[7:0],del2_6[7:0],del2_5[7:0],del2_4[7:0],del2_3[7:0],del2_2[7:0],del2_1[7:0],8'd0,Base2};
end

else 
begin
CoN6 =0;
end
$display ("CoN6 = %b, CCL6 = %h ", CoN6,CCL6);
end






// DECOMPRESSOR BLOCK

always @ (posedge clock)
begin

//BASE 8 DEL 1 BYTE
if(CoN1==1)
begin
CompressedCache = CCL1;
DeCompressedCache[63:0] = CompressedCache[63:0]; //Base8

if (mark8_1 ==0)
DeCompressedCache[127:64] = Base8 - CompressedCache[79:72];
else 
DeCompressedCache[127:64] = CompressedCache[79:72]- Base8 ;

if (mark8_2 ==0)
DeCompressedCache[191:128] = Base8 - CompressedCache[87:80];
else 
DeCompressedCache[191:128] = CompressedCache[87:80]- Base8 ;

if (mark8_3 ==0)
DeCompressedCache[255:192] = Base8 - CompressedCache[95:88];
else 
DeCompressedCache[255:192] = CompressedCache[95:88]- Base8 ;

end

//BASE 8 DEL 2 BYTES
else if(CoN2==1)
begin
CompressedCache = CCL2;
DeCompressedCache[63:0] = CompressedCache[63:0]; //Base8

if (mark8_1 ==0)
DeCompressedCache[127:64] = Base8 - CompressedCache[95:80];
else 
DeCompressedCache[127:64] = CompressedCache[95:80]- Base8 ;

if (mark8_2 ==0)
DeCompressedCache[191:128] = Base8 - CompressedCache[111:96];
else 
DeCompressedCache[191:128] = CompressedCache[111:96]- Base8 ;

if (mark8_3 ==0)
DeCompressedCache[255:192] = Base8 - CompressedCache[127:112];
else 
DeCompressedCache[255:192] = CompressedCache[127:112]- Base8 ;

end

//BASE 8 DEL 4 BYTES
else if(CoN3==1)
begin
CompressedCache = CCL3;
DeCompressedCache[63:0] = CompressedCache[63:0]; //Base8

if (mark8_1 ==0)
DeCompressedCache[127:64] = Base8 - CompressedCache[127:96];
else 
DeCompressedCache[127:64] = CompressedCache[127:96]- Base8 ;

if (mark8_2 ==0)
DeCompressedCache[191:128] = Base8 - CompressedCache[159:128];
else 
DeCompressedCache[191:128] = CompressedCache[159:128]- Base8 ;

if (mark8_3 ==0)
DeCompressedCache[255:192] = Base8 - CompressedCache[191:160];
else 
DeCompressedCache[255:192] = CompressedCache[191:160]- Base8 ;
end

//BASE 4 DEL 1 BYTE
else if(CoN4==1)
begin
CompressedCache = CCL4; //96 BITS
DeCompressedCache[31:0] = CompressedCache[31:0]; //Base4

if (mark4_1 == 0)
DeCompressedCache[63:32] = Base4 - CompressedCache[47:40];
else 
DeCompressedCache[63:32] = CompressedCache[47:40]- Base4 ;

if (mark4_2 == 0)
DeCompressedCache[95:64] = Base4 - CompressedCache[55:48];
else 
DeCompressedCache[95:64] = CompressedCache[55:48]- Base4 ;

if (mark4_3 == 0)
DeCompressedCache[127:96] = Base4 - CompressedCache[63:56];
else 
DeCompressedCache[127:96] = CompressedCache[63:56]- Base4 ;

if (mark4_4 == 0)
DeCompressedCache[159:128] = Base4 - CompressedCache[71:64];
else 
DeCompressedCache[159:128] = CompressedCache[71:64]- Base4 ;

if (mark4_5 == 0)
DeCompressedCache[191:160] = Base4 - CompressedCache[79:72];
else 
DeCompressedCache[191:160] = CompressedCache[79:72]- Base4 ;

if (mark4_6 == 0)
DeCompressedCache[223:192] = Base4 - CompressedCache[87:80];
else 
DeCompressedCache[223:192] = CompressedCache[87:80]- Base4 ;

if (mark4_7 == 0)
DeCompressedCache[255:224] = Base4 - CompressedCache[95:88];
else 
DeCompressedCache[255:224] = CompressedCache[95:88]- Base4 ;
end

//BASE 4 DEL 2 BYTES
else if(CoN5==1)
begin
CompressedCache = CCL5; //160 BITS
DeCompressedCache[31:0] = CompressedCache[31:0]; //Base4

if (mark4_1 == 0)
DeCompressedCache[63:32] = Base4 - CompressedCache[63:48];
else 
DeCompressedCache[63:32] = CompressedCache[63:48]- Base4 ;

if (mark4_2 == 0)
DeCompressedCache[95:64] = Base4 - CompressedCache[79:64];
else 
DeCompressedCache[95:64] = CompressedCache[79:64]- Base4 ;

if (mark4_3 == 0)
DeCompressedCache[127:96] = Base4 - CompressedCache[95:80];
else 
DeCompressedCache[127:96] = CompressedCache[95:80]- Base4 ;

if (mark4_4 == 0)
DeCompressedCache[159:128] = Base4 - CompressedCache[111:96];
else 
DeCompressedCache[159:128] = CompressedCache[111:96]- Base4 ;

if (mark4_5 == 0)
DeCompressedCache[191:160] = Base4 - CompressedCache[127:112];
else 
DeCompressedCache[191:160] = CompressedCache[127:112]- Base4 ;

if (mark4_6 == 0)
DeCompressedCache[223:192] = Base4 - CompressedCache[143:128];
else 
DeCompressedCache[223:192] = CompressedCache[143:128]- Base4 ;

if (mark4_7 == 0)
DeCompressedCache[255:224] = Base4 - CompressedCache[159:144];
else 
DeCompressedCache[255:224] = CompressedCache[159:144]- Base4 ;
end


//BASE 2 DEL 1 BYTE
else if(CoN6==1)
begin
CompressedCache = CCL6; //144 BITS
DeCompressedCache[15:0] = CompressedCache[15:0]; //Base2

if (mark2_1 == 0)
DeCompressedCache[31:16] = Base2 - CompressedCache[31:24];
else 
DeCompressedCache[31:16] = CompressedCache[31:24]- Base2 ;

if (mark2_2 == 0)
DeCompressedCache[47:32] = Base2 - CompressedCache[39:32];
else 
DeCompressedCache[47:32] = CompressedCache[39:32]- Base2 ;

if (mark2_3 == 0)
DeCompressedCache[63:48] = Base2 - CompressedCache[47:40];
else 
DeCompressedCache[63:48] = CompressedCache[47:40]- Base2 ;

if (mark2_4 == 0)
DeCompressedCache[79:64] = Base2 - CompressedCache[55:48];
else 
DeCompressedCache[79:64] = CompressedCache[55:48]- Base2 ;

if (mark2_5 == 0)
DeCompressedCache[95:80] = Base2 - CompressedCache[63:56];
else 
DeCompressedCache[95:80] = CompressedCache[63:56]- Base2 ;

if (mark2_6 == 0)
DeCompressedCache[111:96] = Base2 - CompressedCache[71:64];
else 
DeCompressedCache[111:96] = CompressedCache[71:64]- Base2 ;

if (mark2_7 == 0)
DeCompressedCache[127:112] = Base2 - CompressedCache[79:72];
else 
DeCompressedCache[127:112] = CompressedCache[79:72]- Base2 ;

if (mark2_8 == 0)
DeCompressedCache[143:128] = Base2 - CompressedCache[87:80];
else 
DeCompressedCache[143:128] = CompressedCache[87:80]- Base2 ;

if (mark2_9 == 0)
DeCompressedCache[159:144] = Base2 - CompressedCache[95:88];
else 
DeCompressedCache[159:144] = CompressedCache[95:88]- Base2 ;

if (mark2_10 == 0)
DeCompressedCache[175:160] = Base2 - CompressedCache[103:96];
else 
DeCompressedCache[175:160] = CompressedCache[103:96]- Base2 ;

if (mark2_11 == 0)
DeCompressedCache[191:176] = Base2 - CompressedCache[111:104];
else 
DeCompressedCache[191:176] = CompressedCache[111:104]- Base2 ;

if (mark2_12 == 0)
DeCompressedCache[207:192] = Base2 - CompressedCache[119:112];
else 
DeCompressedCache[207:192] = CompressedCache[119:112]- Base2 ;

if (mark2_13 == 0)
DeCompressedCache[223:208] = Base2 - CompressedCache[127:120];
else 
DeCompressedCache[223:208] = CompressedCache[127:120]- Base2 ;

if (mark2_14 == 0)
DeCompressedCache[239:224] = Base2 - CompressedCache[135:128];
else 
DeCompressedCache[239:224] = CompressedCache[135:128]- Base2 ;

if (mark2_15 == 0)
DeCompressedCache[255:240] = Base2 - CompressedCache[143:136];
else 
DeCompressedCache[255:240] = CompressedCache[143:136]- Base2 ;
end

//No Compression feasible
else
CompressedCache = UnCompressedCache;


end

endmodule
```

</details>

<details>
<summary>Testbench</summary>

```
module pes_cache_compression_tb;

	
	reg clock;
	reg [255:0] UnCompressedCache;
	wire [255:0] CompressedCache,DeCompressedCache;
	wire [95:0]CCL1,CCL4;
wire [127:0]CCL2;
wire [191:0]CCL3;
wire [159:0]CCL5;
wire [143:0]CCL6;
	

	
	CompressorUnit one (
		.clock(clock), 
		.UnCompressedCache(UnCompressedCache), 
		.CompressedCache(CompressedCache),
		.DeCompressedCache(DeCompressedCache)
	);

	initial begin
		
		clock = 0;
		UnCompressedCache = {64'h0000000000000066,64'h0000000000000044,64'h0000000000000022,64'h00000000000000FF};
		#50 UnCompressedCache = {64'h0000000000005566,64'h0000000000003344,64'h0000000000001122,64'h0000000000000000};
		#50 UnCompressedCache = {64'h0000000011225566,64'h0000000011223344,64'h0000000011221122,64'h0000000000000000};
		#50 UnCompressedCache = {32'h00000088,32'h00000077,32'h00000066,32'h00000055,32'h00000044,32'h00000033,32'h00000022,32'h00000000};
		#50 UnCompressedCache = {32'h00007788,32'h00006677,32'h00005566,32'h00004455,32'h00003344,32'h00002233,32'h00001122,32'h00000000};
		#50 UnCompressedCache = {16'h0085,16'h0080,16'h0075,16'h0070,16'h0065,16'h0060,16'h0055,16'h0050,16'h0040,16'h0035,16'h0030,16'h0025,16'h0020,16'h0015,16'h0010,16'h0000};
	end
    
always
#5 clock = ~clock;	 
endmodule

```

</details>
<details>
<summary>Installation of Yosys and GTK wave</summary>

`git clone https://github.com/YosysHQ/yosys.git `

`cd yosys`

`sudo apt install make`

`sudo apt-get update`

`sudo apt-get install build-essential clang bison flex  libreadline-dev gawk tcl-dev libffi-dev git  graphviz xdot pkg-config python3 libboost-system-dev libboost-python-dev libboost-filesystem-dev zlib1g-dev`

*Comment the export path in bashrc for the code given below to work*

`make config-gcc`

`make -j 4`

`sudo make install`

`sudo apt install gtkwave`

</details>

<details>
<summary>Synthesis</summary>
	
## Synthesis

+ Commands
  `yosys`

  `read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib`

  `read_verilog cache_compression.v`

  `synth -top cache_compression`

  `abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib`

  `show`

  `write_verilog -noattr pes_ripple_counter_net.v`

  `iverilog ../verilog_model/primitives.v ../verilog_model/sky130_fd_sc_hd.v pes_cache_compression_net.v pes_cache_compression_tb.v`

  `gtkwave simulation.vcd`

+ Screenshots

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/0d7e43bb-454f-4338-ab20-0a293b761a64)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/df5cd511-7d06-4087-9277-35456f41e294)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/4775b5f4-946e-421c-8eac-d5a13a3f8c6e)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/af9c10e0-f663-4904-955d-9ba732bef21a)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/5a4a231f-08da-43c4-a441-1e22008e85fa)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/f3ac1ac4-989a-4481-80c7-52ab18cf5743)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/48d7f0a3-7925-4419-a582-dce3f92d6053)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/e66ff81f-d342-4c3f-86f1-34ef523a615e)

  
  **As there are more than 20000 cells block diagram is not showing up on terminal**
  
  **Source Google**
  
  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/7311802a-3f50-4ce3-8462-290a8d75d71e)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/4d89eedb-49cd-4639-95d4-8cfe56617897)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/56239f97-7d14-4f6b-bc7e-02e8698fdcbc)

  
</details>

<details>
<summary>GLS(Gate Level Simulation)</summary>

+ Commands
  `cd vlsi/sky130RTLDesignAndSynthesisWorkshop/verilog_files`

  `iverilog cache_compression.v pes_cache_compression_tb.v`

  `./a.out`

  `gtkwave simulation.vcd`

+ Screenshots

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/9834d7b5-776f-4f1e-8f28-d81ad1797a34)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/24d87ca9-1515-4a84-bcfe-bda441b1e657)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/191b2739-6e24-4419-a8e6-05c3266a6261)

</details>
<details>
<summary>Result</summary>
	
+ 8 cache lines of each type were included in the uncompressed data file to get the below result:
+ No. of uncompressed lines = 40
+ No. of uncompressed bytes = 2.58KB
+ No. of compressed bytes= 1.25KB
+ Compression Ratio = 2.064
</details>



# *OpenLane Flow*

 <details>
<summary>Installation of ngspice, magic and OpenLANE</summary>

**Commands**

`
cd $HOME
sudo apt-get install libxaw7-dev
tar -zxvf ngspice-41.tar.gz
cd ngspice-41
mkdir release
cd release
../configure  --with-x --with-readline=yes --disable-debug
sudo make
sudo make install
`

**ngspice magic**

`
sudo apt-get install m4
sudo apt-get install tcsh
sudo apt-get install csh
sudo apt-get install libx11-dev
sudo apt-get install tcl-dev tk-dev
sudo apt-get install libcairo2-dev
sudo apt-get install mesa-common-dev libglu1-mesa-dev
sudo apt-get install libncurses-dev
git clone https://github.com/RTimothyEdwards/magic
cd magic
./configure
sudo make
sudo make install
`

**OpenLANE**

```
sudo apt-get update
sudo apt-get upgrade
sudo apt install -y build-essential python3 python3-venv python3-pip make git

sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io
sudo docker run hello-world
sudo groupadd docker
sudo usermod -aG docker $USER
sudo reboot 
```

**To install the PDKs and Tools**

`
cd $HOME
git clone https://github.com/The-OpenROAD-Project/OpenLane
cd OpenLane
make
make test
`
</details>

<details>
	<summary>Steps to invoke OpenLane</summary>

## Steps to be followed:

+ Create a folder in design - pes_cache_compression
+ Enter your designs folder
+ create a config.json file by typing `./flow.tcl -design pes_cache_compression -init_design_config -add_to_designs` after `make mount`
+ make a new directory called 'src'
+ Enter src folder
  
  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/90b7b5a6-9326-4c49-94b3-c417633d0af7)
  
+ Add your design file & sky130_fd_sc_hd__(fast-slow-typical) files

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/13b12c4e-d4a8-4cfa-b8b0-e6b97d5d490c)

+ Now in the main 'Openlane' directory type **mkdir pdks**.
+ copy and paste sky130_fd_sc_hd.v in it
+ type command `make mount`

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/13b12c4e-d4a8-4cfa-b8b0-e6b97d5d490c)

+ then type `./flow.tcl -interactive`
+ Type `prep -design cache_compression` To prep the design type

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/b0e27634-09d5-47ec-812a-0138048c27eb)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/80da9f6a-6753-4497-ae28-877968f59d95)
</details>
<details>
<summary>Synthesis</summary>

## Synthesis

+ Type `run_synthesis`

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/2f6f7822-08d7-4c7e-853c-f17d0269feed)

  **1-yosys_4.stat.rpt**

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/0c35a23a-d761-41d6-a37e-e0208a66b1ac)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/1dbac1d1-5f3c-4285-a818-96771e2293bb)

  **1-yosys_dff.stat**

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/19702638-322f-4b24-880b-e1304faefa83)

  **1-yosys_pre.stat**

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/0cb9bcd0-a8c8-4444-8630-250a5a6e49b4)

  **2-opensta.min_max.rpt**

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/7a8c5d75-a654-486a-85ef-ff4806e398e8)

  **2-opensta.slew.rpt**

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/6697287c-9f1b-4610-8abb-f33af6c23f2e)

+ Calculating Flop Ratio
  
  ```
  
  Flop ratio = Number of D Flip flops 
               ______________________
               Total Number of cells

  Flop Ratio = 0.06
  ```
</details>
<details>
	<summary>Floorplan</summary>
	
## Floorplan
  Physical design is process of transforming netlist into layout which is manufacture-able [GDS]. Physical design process is often referred as PnR (Place and Route) / APR (Automatic Place & Route). 
  Main steps in physical design are placement of all logical cells, clock tree synthesis & routing. During this process of physical design timing, power, design & technology constraints have to be 
  met. Further design might require being optimized w.r.t area, power and performance
  
+ Invoke floorplan using command `run_floorplan`

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/c8b44853-e4da-4a34-82b7-c7e082a92ebc)


  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/df09d40a-a828-400e-b0b9-80667bcd89ba)

  + Die Area

    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/56749d1c-8a1e-4145-9a7f-6f571b1f7e33)

  + Core Area

    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/86dddaf5-9ffc-4c8f-8262-c0e1fef115ba)

  + End Cap and Tap Cells
 
    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/6415552c-56e9-4989-919c-c740a0fb41f4)

+ Open Floorplan in the magic tool

  `magic -T /home/Vsduser/Desktop/OpenLane/pdks/sky130A/libs.tech/magic/sky130A.tech read ../../tmp/merged_unpadded.lef def read cache_compression.floorplan.def &  `

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/03d32d09-a166-49a9-85f4-cabb0399030e)

  - In the layout, many i/o pins can be seen at the border of the layout, which are equidistant from each other by default.
  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/4a6bd743-0465-4994-9700-0e1f62ee8f70)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/e4274c85-d83e-47eb-a42d-148e1f596b91)

  - Decompressed cache

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/65a089a4-7491-4b7a-9b36-1154d8748f71)

  - UnCompressed cache
 
  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/4a714673-fb4f-4471-8aa7-339e5fa04e5b)

  
</details>
<details>
<summary>Placement</summary>
	
+ Invoke placement using command `run_placement`

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/b49b2e04-4d51-465f-8ac5-9b99f83439ef)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/bcca2b71-289e-4de3-92e0-f9a9d4838639)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/0dccfe7e-5084-43f5-aa38-14cf76800c7c)


  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/6f3cb8ec-3d85-402f-beea-a3e44d8b0a8b)

+ To invoke magic tool type  `magic -T /home/Vsduser/Desktop/OpenLane/pdks/sky130A/libs.tech/magic/sky130A.tech read ../../tmp/merged_unpadded.lef def read cache_compression.placement.def &`

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/de7521d4-31a7-421d-a156-f230e8fcea82)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/01aefd74-acd0-47b8-8428-581b6f38f514)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/56b2a237-7553-40e2-b497-e3a1c2a862ea)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/72d338ee-bbba-4c22-8478-fc4ddcb9463b)

</details>
<details>
	<summary>Clock Tree Synthesis</summary>
	
## Clock Tree Synthesis(CTS):
  Clock Tree Synthesis (CTS) is one of the most important stages in PnR. CTS QoR decides timing convergence & power. In most of the ICs clock consumes 30-40 % of total power. So efficient clock 
  architecture, clock gating & clock tree implementation helps to reduce power
  
  - Type the command `run_cts`
    
    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/d9310763-7beb-4417-872d-222d7b188afb)

    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/f5dc9efa-fd6f-43ea-89f8-4c2d1f404233)

    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/f4a117cc-9438-423d-a51b-054921916d27)

    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/780eacdf-ac05-4d37-b2ab-15c6a3d0a796)

    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/61793125-27b6-4fb3-b0aa-26e2714a86d0)
    
  - To invoke magic tool type  `magic -T /home/Vsduser/Desktop/OpenLane/pdks/sky130A/libs.tech/magic/sky130A.tech read ../../tmp/merged_unpadded.lef def read cache_compression.cts.def &`

    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/b0b39ead-c3f3-4ac4-a045-2ec061466728)

    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/8b0a3f33-6086-4fb8-a456-975082836e4e)

    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/f4b270e2-421d-4256-bc30-90659417e8fa)

    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/a058ab3b-cb3e-4efb-bd7c-815823196d62)




</details>
<details>
<summary>Routing</summary>

## Routing
  Routing is the stage after Clock Tree Synthesis and optimization where-
  Exact paths for the interconnection of standard cells and macros and I/O pins are determined.
  Electrical connections using metals and vias are created in the layout, defined by the logical connections present in the netlist.
  After CTS, we have information of all the placed cells, blockages, clock tree buffers/inverters and I/O pins. The tool relies on this information to electrically complete all connections defined in 
  the netlist such that-

  There are minimal DRC violations while routing.
  
  The design is 100% routed with minimal LVS violations.
  
  There are minimal SI related violations.
  
  There must be no or minimal congestion hot spots.
  
  The Timing DRCs are met.
  
  The Timing QoR is good.
  
  Routing is performed in two stages:

  Fast route - Implemented using FastROAD. It generates routing guides.
  
  Detailed route - Implemented using TritonRoute. It uses the routing guides generated in fast route to find the best route and makes connections.
  
+ Invoke routing by `run_routing`

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/68d3e1d1-fff5-4b4d-8307-da46d40a417e)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/97e34727-2a61-4aea-a70a-5a999bffcdb8)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/8a5093b2-19b4-4a54-b4de-8c732d55055d)

  - 0th optimization iteration(42888 violation)

    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/a85d5bf9-e53e-43af-9081-e39c548bb519)

 - 1st optimazation iteration (22465 violations)

   ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/8f842459-cece-4573-82d3-fac7de22c2ae)

  - 2nd optimazation iteration (21561 violations)

    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/bed3e731-7bc9-4cd4-8f62-35d7ec057b49)

  - 3rd optimazation iteration(2802 violations)

    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/e66cc1f9-c0de-40b3-a18e-b3e14d2ae701)


  - 4th optimazation iteration(246 violations)
 
    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/57b3ab36-166c-4c1b-9458-fb1cbf8a26e5)

  - 5th optimazation iteration(36 violations) and so one till 57th iteration
 
    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/c69b3a8c-397f-49a9-9ea2-7e096c3caa3d)

    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/1ed769e0-08e3-4fae-b512-bbce698e5bd3)
    
  - 57th optimization iteration(0 violations)

    ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/158f6d04-f404-411f-9761-ec854c9ffaf2)

+ complete detail routing

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/03c24a24-5d7e-4231-a647-04918cc7eda7)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/85e80776-43cc-4cdd-ad73-c96fcd09053c)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/20cb1236-d9a3-4697-a60b-754b9f61b853)

+ To invoke magic tool type  `magic -T /home/Vsduser/Desktop/OpenLane/pdks/sky130A/libs.tech/magic/sky130A.tech read ../../tmp/merged_unpadded.lef def read cache_compression.def &`

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/c6111d18-e7b7-4ee5-a2e1-68808ee62451)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/d0922590-ea59-4285-beeb-c75d92afe660)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/66a6b35d-9c52-4b47-a897-886bcef9772b)

  ![image](https://github.com/ShashidharReddy01/pes_cache_compression/assets/142148810/25a58ac8-ca6e-4efb-ac57-b21b6f6b3dc1)


```
  First L Route
[INFO] WIRELEN : 224999, WIRELEN1 : 224999
[INFO] NumSeg  : 49161
[INFO] NumShift: 1538
[Overflow Report] Total hCap    : 267521
[Overflow Report] Total vCap    : 212550
[Overflow Report] Total Usage   : 224999
[Overflow Report] Max H Overflow: 0
[Overflow Report] Max V Overflow: 1
[Overflow Report] Max Overflow  : 1
[Overflow Report] Num Overflow e: 1
[Overflow Report] H   Overflow  : 0
[Overflow Report] V   Overflow  : 1
[Overflow Report] Final Overflow: 1

Second L Route
[Overflow Report] Total hCap    : 267521
[Overflow Report] Total vCap    : 212550
[Overflow Report] Total Usage   : 224999
[Overflow Report] Max H Overflow: 0
[Overflow Report] Max V Overflow: 0
[Overflow Report] Max Overflow  : 0
[Overflow Report] Num Overflow e: 0
[Overflow Report] H   Overflow  : 0
[Overflow Report] V   Overflow  : 0
[Overflow Report] Final Overflow: 0

First Z Route
[Overflow Report] Total hCap    : 267521
[Overflow Report] Total vCap    : 212550
[Overflow Report] Total Usage   : 224999
[Overflow Report] Max H Overflow: 0
[Overflow Report] Max V Overflow: 0
[Overflow Report] Max Overflow  : 0
[Overflow Report] Num Overflow e: 0
[Overflow Report] H   Overflow  : 0
[Overflow Report] V   Overflow  : 0
[Overflow Report] Final Overflow: 0

[INFO] LV routing round 0, enlarge 10
[INFO] 10 threshold, 10 expand
[Overflow Report] total Usage   : 224999
[Overflow Report] Max H Overflow: 33
[Overflow Report] Max V Overflow: 10
[Overflow Report] Max Overflow  : 33
[Overflow Report] Num Overflow e: 556
[Overflow Report] H   Overflow  : 1832
[Overflow Report] V   Overflow  : 918
[Overflow Report] Final Overflow: 2750

[INFO] LV routing round 1, enlarge 15
[INFO] 5 threshold, 15 expand
[Overflow Report] total Usage   : 225411
[Overflow Report] Max H Overflow: 0
[Overflow Report] Max V Overflow: 2
[Overflow Report] Max Overflow  : 2
[Overflow Report] Num Overflow e: 5
[Overflow Report] H   Overflow  : 0
[Overflow Report] V   Overflow  : 6
[Overflow Report] Final Overflow: 6

[INFO] LV routing round 2, enlarge 20
[INFO] 1 threshold, 20 expand
[Overflow Report] total Usage   : 225399
[Overflow Report] Max H Overflow: 0
[Overflow Report] Max V Overflow: 1
[Overflow Report] Max Overflow  : 1
[Overflow Report] Num Overflow e: 1
[Overflow Report] H   Overflow  : 0
[Overflow Report] V   Overflow  : 1
[Overflow Report] Final Overflow: 1

Running extra iterations to remove overflow...
Update congestion history type 1
[INFO] iteration 1, enlarge 20, costheight 9, threshold 10 via cost 2
[INFO] log_coef 2.000000, healingTrigger 0 cost_step 5 L 1 cost_type 1 updatetype 1
[Overflow Report] total Usage   : 225404
[Overflow Report] Max H Overflow: 0
[Overflow Report] Max V Overflow: 0
[Overflow Report] Max Overflow  : 0
[Overflow Report] Num Overflow e: 0
[Overflow Report] H   Overflow  : 0
[Overflow Report] V   Overflow  : 0
[Overflow Report] Final Overflow: 0

Usage checked
Maze routing finished
[INFO] P3 runtime: 0.010000 sec
[INFO] Final 2D results:
[Overflow Report] total Usage   : 225404
[Overflow Report] Max H Overflow: 0
[Overflow Report] Max V Overflow: 0
[Overflow Report] Max Overflow  : 0
[Overflow Report] Num Overflow e: 0
[Overflow Report] H   Overflow  : 0
[Overflow Report] V   Overflow  : 0
[Overflow Report] Final Overflow: 0

Layer Assignment Begins
Layer assignment finished
[INFO] 2D + Layer Assignment Runtime: 3.140000 sec
Post Processing Begins
Post Processsing finished
 Starting via filling
[INFO] Via related to pin nodes 97899
[INFO] Via related stiner nodes 5764
Via filling finished

Final usage/overflow report:
[INFO] Usage per layer:
    Layer 1 usage: 0
    Layer 2 usage: 84724
    Layer 3 usage: 85575
    Layer 4 usage: 34208
    Layer 5 usage: 21208
    Layer 6 usage: 0

[INFO] Capacity per layer:
    Layer 1 capacity: 0
    Layer 2 capacity: 154646
    Layer 3 capacity: 146068
    Layer 4 capacity: 101640
    Layer 5 capacity: 66482
    Layer 6 capacity: 11235

[INFO] Use percentage per layer:
    Layer 1 use percentage: 0.0%
    Layer 2 use percentage: 54.79%
    Layer 3 use percentage: 58.59%
    Layer 4 use percentage: 33.66%
    Layer 5 use percentage: 31.90%
    Layer 6 use percentage: 0.00%

[INFO] Overflow per layer:
    Layer 1 overflow: 0
    Layer 2 overflow: 0
    Layer 3 overflow: 0
    Layer 4 overflow: 0
    Layer 5 overflow: 0
    Layer 6 overflow: 0

[Overflow Report] Total Usage   : 225715
[Overflow Report] Total Capacity: 480071
[Overflow Report] Max H Overflow: 0
[Overflow Report] Max V Overflow: 0
[Overflow Report] Max Overflow  : 0
[Overflow Report] H   Overflow  : 0
[Overflow Report] V   Overflow  : 0
[Overflow Report] Final Overflow: 0

[INFO] Final usage          : 225715
[INFO] Final number of vias : 115328
[INFO] Final usage 3D       : 571699
[INFO GRT-0018] Total wirelength: 1992181 um

```
</details>




## Acknowlegedgements

- [Prof Mahesh Awati](https://in.linkedin.com/in/mahesh-awati-4423538b) Associate professor, PES University
- [Kunal Ghosh](https://github.com/kunalg123), Co-founder, VSD Corp. Pvt. Ltd.
- [Steve Hoover](https://github.com/stevehoover), Founder, Redwood EDA

## Contact Information

- Kunal Ghosh, Director, VSD Corp. Pvt. Ltd. (Email: kunalghosh@gmail.com)
- Steve Hoover, Founder, Redwood EDA (Email: steve.hoover@redwoodeda.com)
- Shashidhar V Reddy ,Student, PES University (Email : pes2202100627@pesu.pes.edu)

