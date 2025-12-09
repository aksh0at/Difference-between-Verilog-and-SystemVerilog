module traffic_light_v (
    input wire clk,
    input wire rst_n,
    input wire sensor_input,
    output reg [1:0] light_out // 00=Red, 01=Yellow, 10=Green
);

    // 1. Manual State Encoding (Prone to errors if values overlap)
    parameter RED    = 2'b00;
    parameter GREEN  = 2'b10;
    parameter YELLOW = 2'b01;

    // 2. Confusing Data Types: Must use 'reg' for procedural blocks
    reg [1:0] current_state, next_state;

    // 3. Sequential Logic
    always @(posedge clk or negedge rst_n) begin
        if (!rst_n)
            current_state <= RED;
        else
            current_state <= next_state;
    end

    // 4. Combinational Logic (Generic 'always' block)
    // RISK: If we forget a signal in the sensitivity list (or use @* incorrectly),
    // simulation mismatches or latches occur.
    always @(*) begin
        // Default assignment to prevent latches
        next_state = current_state; 
        light_out = 2'b00;

        case (current_state)
            RED: begin
                light_out = 2'b00; // Red output
                if (sensor_input) next_state = GREEN;
            end
            GREEN: begin
                light_out = 2'b10; // Green output
                next_state = YELLOW;
            end
            YELLOW: begin
                light_out = 2'b01; // Yellow output
                next_state = RED;
            end
            default: next_state = RED;
        endcase
    end

endmodule

