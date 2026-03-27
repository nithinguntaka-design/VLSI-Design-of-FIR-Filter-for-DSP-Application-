# VLSI-Design-of-FIR-Filter-for-DSP-Application-
VLSI Design of FIR Filter for DSP Application 
MATLAB Code (Generate Coefficients)
% FIR Filter Design
clc;
clear;

N = 8;              % Filter order
Fc = 0.3;           % Cutoff frequency (0 to 1)

% Generate coefficients using window method
h = fir1(N, Fc, 'low');

disp('Filter Coefficients:');
disp(h);

% Plot response
fvtool(h,1);

Verilog Implementation (FIR Filter)
module fir_filter(
    input clk,
    input rst,
    input signed [15:0] x_in,
    output reg signed [31:0] y_out
);

// Coefficients (from MATLAB)
parameter h0 = 16'd2;
parameter h1 = 16'd8;
parameter h2 = 16'd20;
parameter h3 = 16'd30;
parameter h4 = 16'd30;
parameter h5 = 16'd20;
parameter h6 = 16'd8;
parameter h7 = 16'd2;

// Shift register
reg signed [15:0] x [0:7];
integer i;

always @(posedge clk or posedge rst) begin
    if (rst) begin
        for (i=0; i<8; i=i+1)
            x[i] <= 0;
    end else begin
        x[0] <= x_in;
        for (i=1; i<8; i=i+1)
            x[i] <= x[i-1];
    end
end

// MAC Operation
always @(posedge clk) begin
    y_out <= (h0*x[0]) + (h1*x[1]) + (h2*x[2]) + (h3*x[3]) +
             (h4*x[4]) + (h5*x[5]) + (h6*x[6]) + (h7*x[7]);
end

endmodule


Testbench (Simulation)
`timescale 1ns/1ps

module tb_fir;

reg clk;
reg rst;
reg signed [15:0] x_in;
wire signed [31:0] y_out;

fir_filter uut(
    .clk(clk),
    .rst(rst),
    .x_in(x_in),
    .y_out(y_out)
);

always #5 clk = ~clk;

initial begin
    clk = 0;
    rst = 1;
    x_in = 0;

    #10 rst = 0;

    // Input samples
    #10 x_in = 10;
    #10 x_in = 20;
    #10 x_in = 30;
    #10 x_in = 40;
    #10 x_in = 50;

    #100 $stop;
end

endmodule

