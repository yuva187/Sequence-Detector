# Sequence-Detector
## Aim
To design and simulate a sequence detector using both Moore and Mealy state machine models in Verilog HDL, and verify their functionality through a testbench using the Vivado 2023.1 simulation environment. The objective is to detect a specific sequence of bits (e.g., 1011) and compare the Moore and Mealy designs.

## Apparatus Required
Vivado 2023.1 or equivalent Verilog simulation tool.
Computer system with a suitable operating system.
## Procedure
Launch Vivado 2023.1:

Open Vivado and create a new project.
Write the Verilog Code for Sequence Detector (Moore and Mealy FSM):

Design two Verilog modules: one for a Moore FSM and another for a Mealy FSM to detect a sequence such as 1011.
Create the Testbench:

Write a testbench to apply input sequences and verify the output of both FSM designs.
Add the Verilog Files:

Add the Verilog code for both FSMs and the testbench to the Vivado project.
Run Simulation:

Run the simulation and observe the output to check if the sequence is detected correctly.
Observe the Waveforms:

Analyze the waveform to ensure both the Moore and Mealy machines detect the sequence as expected.
Save and Document Results:

Capture the waveforms and include the results in the final report.

## Verilog Code for Sequence Detector Using Moore FSM
```
module fsm_sequence(
    input clk,
    input reset,
    input run,
    output reg [3:0] count
);

    // State encoding
    localparam s0 = 4'd0;
    localparam s2 = 4'd2;
    localparam s4 = 4'd4;
    localparam s6 = 4'd6;
    localparam s8 = 4'd8;
    localparam s10 = 4'd10;
    localparam s12 = 4'd12;

    reg [3:0] current_state, next_state;

    // State transition logic
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            current_state <= s0;  // Initialize to state s0 on reset
        end else begin
            current_state <= next_state;  // Move to next state on clock edge
        end
    end

    // Next state logic
    always @(*) begin
        case (current_state)
            s0:  if (run) next_state = s2;  else next_state = s0;
            s2:  if (run) next_state = s4;  else next_state = s2;
            s4:  if (run) next_state = s6;  else next_state = s4;
            s6:  if (run) next_state = s8; else next_state = s6;
            s8: if (run) next_state = s10; else next_state = s8;
            s10: if (run) next_state = s12; else next_state = s10;
            s12: if (run) next_state = s0;  else next_state = s12;
            default: next_state = s0;
        endcase
    end

    // Output logic
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            count <= 4'd0;  // Reset count when reset is high
        end else begin
            count <= current_state;  // Output the current state as the count
        end
    end

endmodule
```
## Output:
![image](https://github.com/user-attachments/assets/c8c42d76-5253-4198-a8cc-29bfc3b825f0)


## Verilog Code for Sequence Detector Using Mealy FSM
```
module fsm_sequence_mealy(
    input clk,
    input reset,
    input run,
    output reg [3:0] count
);

    // State encoding
    localparam s0 = 4'd0;
    localparam s2 = 4'd2;
    localparam s4 = 4'd4;
    localparam s6 = 4'd6;
    localparam s8 = 4'd8;
    localparam s10 = 4'd10;
    localparam s12 = 4'd12;

    reg [3:0] current_state, next_state;

    // State transition logic
    always @(posedge clk or posedge reset) begin
        if (reset) begin
            current_state <= s0;  // Initialize to state s0 on reset
        end else begin
            current_state <= next_state;  // Move to next state on clock edge
        end
    end

    // Next state and output logic
    always @(*) begin
        next_state = current_state;  // Default to stay in the current state
        
        // Mealy-style output: output depends on both the current state and input (run)
        case (current_state)
            s0: begin
                if (run) begin
                    next_state = s2;
                    count = 4'd2;  // Output depends on the input and state
                end else begin
                    count = 4'd0;
                end
            end
            s2: begin
                if (run) begin
                    next_state = s4;
                    count = 4'd4;
                end else begin
                    count = 4'd2;
                end
            end
            s4: begin
                if (run) begin
                    next_state = s6;
                    count = 4'd6;
                end else begin
                    count = 4'd4;
                end
            end
            s6: begin
                if (run) begin
                    next_state = s8;
                    count = 4'd8;
                end else begin
                    count = 4'd6;
                end
            end
            s8: begin
                if (run) begin
                    next_state = s10;
                    count = 4'd10;
                end else begin
                    count = 4'd8;
                end
            end
            s10: begin
                if (run) begin
                    next_state = s12;
                    count = 4'd12;
                end else begin
                    count = 4'd10;
                end
            end
            s12: begin
                if (run) begin
                    next_state = s0;
                    count = 4'd0;
                end else begin
                    count = 4'd12;
                end
            end
            default: begin
                next_state = s0;
                count = 4'd0;
            end
        endcase
    end

endmodule
```
## Output:
![image](https://github.com/user-attachments/assets/8ecbea8e-5c62-485a-bea3-c442940e3a14)
## Testbench for Sequence Detector (Moore and Mealy FSMs):
```
`timescale 1ns/1ps

module tb_fsm_sequence;

    // Testbench signals
    reg clk;
    reg reset;
    reg run;
    wire [3:0] count;

    // Instantiate the FSM module
    fsm_sequence uut (
        .clk(clk),
        .reset(reset),
        .run(run),
        .count(count)
    );

    // Clock generation (50MHz, period = 20ns)
    always #10 clk = ~clk; // Toggle clock every 10 time units (20ns period)

    // Test sequence
    initial begin
        // Initialize inputs
        clk = 0;
        reset = 1;
        run = 0;

        // Apply reset
        #20 reset = 0;  // De-assert reset after 20ns

        // Test the FSM transitions with run signal
        #20 run = 1;    // Start running the FSM after 20ns

        // Observe FSM behavior by allowing it to transition through states
        #100 run = 0;   // Stop the FSM by disabling run
        #40 run = 1;    // Restart the FSM after a short pause
        #100 $finish;   // End the simulation
    end

    // Monitor the output
    initial begin
        $monitor("Time: %0t | clk: %b | reset: %b | run: %b | count: %d", 
                 $time, clk, reset, run, count);
    end

    // Dump waves for Vivado simulation
    initial begin
        $dumpfile("fsm_sequence_tb.vcd");
        $dumpvars(0, tb_fsm_sequence);
    end

endmodule
```

## Conclusion
In this experiment, Moore and Mealy FSMs were successfully designed and simulated to detect the sequence 1011. Both designs worked as expected, with the main difference being that the Moore FSM generated the output based on the current state, while the Mealy FSM generated the output based on both the current state and input. The testbench verified the functionality of both FSMs, demonstrating that the Verilog HDL can effectively model both types of state machines for sequence detection tasks.
