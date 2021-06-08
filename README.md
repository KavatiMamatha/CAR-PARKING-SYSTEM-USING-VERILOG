# CAR-PARKING-SYSTEM-USING-VERILOG


Tool Used : Vivado 2019.1, Spartan-7

### ABSTRACT

![image](https://user-images.githubusercontent.com/85278130/121148902-ceafad00-c85f-11eb-8915-1382a23cd94c.png)
                
                 FIG1- Simple Concept of Car Parking System


This project is designed to implement a car parking system using Verilog. In this project, at the entrance of the
parking system, a sensor is used to detect the presence of a vehicle. Once the sensor is triggered, a password is
requested to open the gate. If the entered password is correct, the gate opens to let the vehicle in, otherwise,
the gate remains locked. This project is implemented on Spartan 7board

### INTRODUCTION

Drivers searching for parking are estimated to be responsible for about 30% of
traffic congestion in cities. Historically, cities, businesses, and property developers
have tried to match parking supply to growing demand for parking spaces. 

Parking System can be defined as the use of advanced technologies for the better
operation, controlling of traffic, and management of parking within an urban area.
 A number of technologies provide the basis for parking solutions, including vehicle sensors,
wireless communications, and data analytics. Parking System is also made useful by
 recent technology in areas such as mobile appilcation customer services, mobile UPI payments,
and in-car GPS navigation systems. At the heart of the parking system concept is the ability
to access, collect, analyze, disseminate, and act on information on parking usage.
Increasingly, this information is provided in real-time from intelligent devices that
enable both parking managers and drivers to optimize the use of parking capacity.


### Motivation to use Parking System:


1. New Revenue Streams – Many new revenue streams are possible with smart
parking technology. For example, lot owners can enable tiered payment options
dependent on parking space location. Also, reward programs can be integrated
into existing models to encourage repeat users.
2.Integrated Payments and POS – Returning users can replace daily, manual cash 
payments with account invoicing and application payments from their phone.
This could also enable customer loyalty programs and valuable user feedback.

3. Real-Time Data and Trend Insight – Over time, a smart parking solution can
produce data that uncovers correlations and trends of users and lots. These trends
can prove to be invaluable to lot owners as to how to make adjustments and
improvements to drivers.

4. Reduced pollution – Searching for parking burns around one million barrels of oil
a day. An optimal parking solution will significantly decrease driving time, thus
lowering the amount of daily vehicle emissions and ultimately reducing the
global environmental footprint.


5. Enhanced User Experience – A smart parking solution will integrate the entire
user experience into a unified action. Driver’s payment, spot identification,
location search and time notifications all seamlessly become part of the
destination arrival process.


6. Optimized parking – Users find the best spot available, saving time, resources
and effort. The parking lot fills up efficiently and space can be utilized properly
by commercial and corporate entities.

7. Reduced traffic – Traffic flow increases as fewer cars are required to drive around
in search of an open parking space.


8. Increased Safety – Parking lot employees and security guards contain real-time
lot data that can help prevent parking violations and suspicious activity. License
plate recognition cameras can gather pertinent footage. Also, decreased spotsearching traffic on the streets can reduce accidents caused by the distraction of searching for parking.

9. Real-Time Data and Trend Insight – Over time, a smart parking solution can
produce data that uncovers correlations and trends of users and lots. These trends
can prove to be invaluable to lot owners as to how to make adjustments and
improvements to drivers.
10. Decreased Management Costs – More automation and less manual activity saves
  on labor cost and resource exhaustion.
  
 ### Significance
 The smart parking system is considered beneficial for the
car park operators, car park patrons as well as in environment
conservation.

### FSM For Car Parking System

![image](https://user-images.githubusercontent.com/85278130/121150578-47633900-c861-11eb-88db-aeaa7ecb27ba.png)
              
              FIG2:- State Diagram for Car parking System
### Verilog Coding

      `timescale 1ns / 1ps


      module parking_system( 
                 input clock_in,rst_in,
                 input Front_Sensor, Back_Sensor, 
                 input [1:0]  pass_1, pass_2,
                 output wire G_LED,R_LED,
                 output reg [6:0] HEX_1, HEX_2
                   );


       parameter IDLE = 3'b000, WAIT_PASSWORD = 3'b001, WRONG_PASS = 3'b010, RIGHT_PASS = 3'b011,STOP = 3'b100;
           // Moore FSM : output just depends on the current state
           reg[2:0] PS, NS;
           reg[31:0] cnt_wait;
           reg red_tmp,green_tmp;


             // Next state
             always @(posedge clock_in or negedge rst_in)
                 begin
                 if(~rst_in) 
                 PS = IDLE;
                 else
                 PS = NS;
             end


       // cnt_wait
       always @(posedge clock_in or negedge rst_in) 
        begin
           if(~rst_in) 
           cnt_wait <= 0;
           else if(PS==WAIT_PASSWORD)
           cnt_wait <= cnt_wait + 1;
           else 
           cnt_wait <= 0;
        end


       // change state
         always @(*)
         begin
             case(PS)
               IDLE: 
               begin
                if(Front_Sensor == 1)
                  NS = WAIT_PASSWORD;
                else
                  NS = IDLE;
                end

               WAIT_PASSWORD: 
               begin
                if(cnt_wait <= 3)
                  NS = WAIT_PASSWORD;
                  else 
                begin
                 if((pass_1==2'b01)&&(pass_2==2'b10))
                     NS = RIGHT_PASS;
                 else
                   NS = WRONG_PASS;
                 end
                end

               WRONG_PASS: 
               begin
                  if((pass_1==2'b01)&&(pass_2==2'b10))
                  NS = RIGHT_PASS;
                  else
                  NS = WRONG_PASS;
                  end

               RIGHT_PASS: 
               begin
                if(Front_Sensor==1 && Back_Sensor == 1)
                  NS = STOP;
                else if(Back_Sensor == 1)
                  NS = IDLE;
                else
                  NS = RIGHT_PASS;
                end

               STOP: 
               begin
                if((pass_1==2'b01)&&(pass_2==2'b10))
                  NS = RIGHT_PASS;
                else
                  NS = STOP;
                end

               default: NS = IDLE;
          endcase
         end

       // LEDs and output, change the period of blinking LEDs here
           always @(posedge clock_in) begin 

           case(PS)
             IDLE: 
             begin
               green_tmp = 1'b0;
               red_tmp = 1'b0;
               HEX_1 = 7'b1111111; // off
               HEX_2 = 7'b1111111; // off
             end

             WAIT_PASSWORD:
             begin
               green_tmp = 1'b0;
               red_tmp = 1'b1;
               HEX_1 = 7'b000_0110; // E
               HEX_2 = 7'b010_1011; // n 
             end

             WRONG_PASS: 
             begin
               green_tmp = 1'b0;
               red_tmp = ~red_tmp;
               HEX_1 = 7'b000_0110; // E
               HEX_2 = 7'b000_0110; // E 
             end

             RIGHT_PASS: 
             begin
               green_tmp = ~green_tmp;
               red_tmp = 1'b0;
               HEX_1 = 7'b000_0010; // 6
               HEX_2 = 7'b100_0000; // 0 
             end

             STOP: 
             begin
               green_tmp = 1'b0;
               red_tmp = ~red_tmp;
               HEX_1 = 7'b001_0010; // 5
               HEX_2 = 7'b000_1100; // P 
             end
          endcase
        end

          assign R_LED = red_tmp  ;

          assign G_LED = green_tmp;

      endmodule


### Verilog Testbench

            `timescale 1ns / 1ps

            module tb_parking_system;

              // Inputs
              reg clock_in;
              reg rst_in;
              reg Front_Sensor;
              reg Back_Sensor;
              reg [1:0] pass_1;
              reg [1:0] pass_2;

              // Outputs
              wire G_LED;
              wire R_LED;
              wire [6:0] HEX_1;
              wire [6:0] HEX_2;

              // Instantiate the Unit Under Test (UUT)
              parking_system uut (
              .clock_in(clock_in), 
              .rst_in(rst_in), 
              .Front_Sensor(Front_Sensor), 
              .Back_Sensor(Back_Sensor), 
              .pass_1(pass_1), 
              .pass_2(pass_2), 
              .G_LED(G_LED), 
              .R_LED(R_LED), 
              .HEX_1(HEX_1), 
             .HEX_2(HEX_2)
             );

             initial 

             begin
             clock_in = 0;
             forever #10 clock_in = ~clock_in;
             end

             initial 
             begin
             // Initialize Inputs
             rst_in = 0;
             Front_Sensor = 0;
             Back_Sensor = 0;
             pass_1 = 0;
             pass_2 = 0;
             // Wait 100 ns for global reset to finish

             #100;
                  rst_in = 1;
             #20;
             Front_Sensor = 1;
             #1000;

             Front_Sensor = 0;
             pass_1 = 1;

             pass_2 = 2;

             #2000;
             Back_Sensor =1;
             #100
             Front_Sensor = 1;
             // Add stimulus here
            end

            endmodule
            
### Simulation Result 

![image](https://user-images.githubusercontent.com/85278130/121156936-c27b1e00-c866-11eb-8f49-7c403117626a.png)

      FIG3:- Simulation Results of Car Parking System
