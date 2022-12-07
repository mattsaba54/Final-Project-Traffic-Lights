#include <msp430.h> 

volatile unsigned int b1pressed = 0;
volatile unsigned int b2pressed = 0;
volatile unsigned int seconds = 0;
/**
 * main.c
 */
void delay(long int n)
{
    volatile long int i=0;     // Do nothing for n iterations (spin round in the loop)
    for(i=0;i<n;i++)
    {
    }
    return;
}


void setS1(int green, int yellow, int red)
{
    if (green == 1)
    {
        P1OUT |= BIT6;          //G1;
    }
    else
    {
        P1OUT &= ~BIT6;         //G1;
    }
    if (yellow == 1)
    {
        P1OUT |= BIT5;          //Y1;
    }
    else
    {
        P1OUT &= ~BIT5;         //Y1;
    }
    if (red == 1)
    {
        P1OUT |= BIT4;          //R1;
    }
    else
    {
        P1OUT &= ~BIT4;          //R1;
    }
}

void setS2(int green, int yellow, int red)
{
    if (green == 1)
    {
        P5OUT |= BIT3;           //G2;
    }
    else
    {
        P5OUT &= ~BIT3;         //G2;
    }
    if (yellow == 1)
    {
        P5OUT |= BIT1;          //Y2;
    }
    else
    {
        P5OUT &= ~BIT1;         //Y2;
    }
    if (red == 1)
    {
        P5OUT |= BIT0;          //R2;
    }
    else
    {
        P5OUT &= ~BIT0;         //R2;
    }
}

void setS3(int green, int yellow, int red)
{
    if (green == 1)
    {
        P5OUT |= BIT2;          //G3;
    }
    else
    {
        P5OUT &= ~BIT2;         //G3;
    }
    if (yellow == 1)
    {
        P3OUT |= BIT6;          //Y3;
    }
    else
    {
        P3OUT &= ~BIT6;         //Y3;
    }
    if (red == 1)
    {
        P1OUT |= BIT7;          //R3;
    }
    else
    {
        P1OUT &= ~BIT7;         //R3;
    }
}

void setS4(int green, int yellow, int red)
{
    if (green == 1)
    {
        P4OUT |= BIT5;           //G4;
    }
    else
    {
        P4OUT &= ~BIT5;         //G4;
    }
    if (yellow == 1)
    {
        P1OUT |= BIT1;          //Y4;
    }
    else
    {
        P1OUT &= ~BIT1;          //Y4;
    }
    if (red == 1)
    {
        P5OUT |= BIT4;          //R4;
    }
    else
    {
        P5OUT &= ~BIT4;          //R4;
    }
}

int main(void) {

    WDTCTL = WDTPW | WDTHOLD;   // Stop watchdog timer

 /*Setup timer
    TB0CTL |= TBCLR;            //reset timer
    TB0CTL |= TBSSEL_ACLK;      //Clock = ACLK
    TB0CTL |= MC_CONTINUOUS;    //Mode=Continuous
    // Setup TB0 overflow IRQ
    TB0CTL |= TBIE;             //local enable for TB0 Overflow
    __enable_interrupt();       //Enable maskable IRQs
    TB0CTL &= ~TBIFG;           //Clear IRQ Flag */

    P1DIR &= ~BIT2;
    P3DIR &= ~BIT4;



//INTERSECTION STOP1: configuration of ports as output connected to the R1, Y1, G1
   P1DIR |= BIT4;          // Set P1.4 to output direction  R1
   P1DIR |= BIT5;        // Set P1.5 to output direction  Y1
   P1DIR |= BIT6;          // Set P1.6 to output direction  G1

  //INTERSECTION STOP 2: configuration of ports as output connected to  R2, Y2, G2
   P5DIR |= BIT0;          // Set P5.0 to output direction  R2
   P5DIR |= BIT1;        // Set P5.1 to output direction  Y2
   P5DIR |= BIT3;          // Set P5.3 to output direction  G2

    //INTERSECTION STOP 3: configuration of ports as output connected to  R3, Y3, G3
   P1DIR |= BIT7;          // Set P1.7 to output direction  R3
   P3DIR |= BIT6 ;       // Set P3.6 output direction  Y3
   P5DIR |= BIT2;          // Set P5.2 to output direction  G3

    //INTERSECTION STOP 4: configuration of ports as output connected to the R4, Y4, G4
   P5DIR |= BIT4;          // Set P5.4 to output direction  R4
   P1DIR |= BIT1 ;       // Set P1.1 to output direction  Y4
   P4DIR |= BIT5;          // Set P4.5 to output direction  G4

// SEettings the input
   P3DIR &= ~BIT4;
   P1DIR &= ~BIT2;
   //Setting up timer

   TB0CTL |= TBCLR;             // Reset timer
  TB0CTL |= TBSSEL__ACLK;      // Clock = ACKL
  TB0CTL |= MC__CONTINOUS;     // Mode=Continuous

  TB0CTL |= TBIE;         // Local enable for TB0 Overflows



   PM5CTL0 &= ~LOCKLPM5;                   // Disable the GPIO power-on default high-impedance mode
                                                       // to activate previously conIFGured port settings


    // TURN OFF ALL LEDS EXCEPT G1, R2, R3, R4
    P1OUT &= ~BIT4;   //R1
    P1OUT &= ~BIT5;  //Y1


    P5OUT &= ~BIT1; //Y2 is OFF
    P5OUT &= ~BIT3; //G2 is OFF
    P3OUT &= ~BIT6; //Y3 is OFF
    P5OUT &= ~BIT2; //G3 is OFF
    P1OUT &= ~BIT1; //Y4 is OFF
    P4OUT &= ~BIT5; //G4 is OFF

    // interrupt enabled
       P3IES |= BIT4;               //Make sensitivity H-L
       P1IES |= BIT3;               //Make sensitivity H-L


       //-- setup IRQ
       P3IE |= BIT4;                // Enable 3.4 IRQ
       P1IE |= BIT2;                // Enable 1.2 IRQ

       P3IES |=  BIT1;              // Makes sensitivity High-to-Low
       P3IFG &= ~BIT1;             // Clear P4.1 IRQ Flag
       P3IE |= BIT1;               // Enable P4.1 IRQ



       __enable_interrupt();       //Enable Maskable IRQS
       P3IFG &= ~BIT4;             // Clear 3.4 IRQ Flag
       P1IFG &= ~BIT2;             // Clear 1.2 IRQ Flag

       TB0CTL &= ~TBIFG;        // Clear IRQ Flag

       b1pressed = 0;
       b2pressed = 0;


       while (1){

           b1pressed=P3IN & BIT4;



           if(b1pressed == 0){

               setS1(0,0,1);
               setS2(1,0,0);
               setS3(1,0,0);
               setS4(0,0,1);
               delay(100000);   // 20s

               setS1(0,0,1);
               setS2(0,1,0);
               setS3(0,1,0);
               setS4(0,0,1);
               delay(100000);   // 20s

              setS1(1,0,0);
              setS2(0,0,1);
              setS3(0,0,1);
              setS4(1,0,0);
              delay(100000);   // 20s

              setS1(0,1,0);
              setS2(0,0,1);
              setS3(1,0,0);
              setS4(0,0,1);
               delay(100000);   // 20s\

           }else{

               setS1(0,0,1);
                setS2(0,0,1);
                setS3(0,0,1);
                setS4(0,0,1);
                delay(100000);   // 20s
           }
       b2pressed=P1IN & BIT2;

       if(b2pressed == 0){

           setS1(0,0,1);
           setS2(1,0,0);
           setS3(1,0,0);
           setS4(0,0,1);
           delay(100000);   // 20s

           setS1(0,0,1);
           setS2(0,1,0);
           setS3(0,1,0);
           setS4(0,0,1);
           delay(100000);   // 20s

          setS1(1,0,0);
          setS2(0,0,1);
          setS3(0,0,1);
          setS4(1,0,0);
          delay(100000);   // 20s

          setS1(0,1,0);
          setS2(0,0,1);
          setS3(1,0,0);
          setS4(0,0,1);
           delay(100000);   // 20s\

       }else{

           setS1(0,0,1);
            setS2(0,0,1);
            setS3(0,0,1);
            setS4(0,0,1);
            delay(100000);   // 20s
       }
   }

}
//--- ISRs -------------//

#pragma vector = TIMER0_B1_VECTOR
__interrupt void ISR_TB0_Overflow(void){


    seconds += 2;

    if(b1pressed == 1){


        if(seconds == 12){

            b1pressed = 0;
            seconds = 0;
        }
    }else{
        seconds = 0;
    }
    if(b2pressed == 1){


            if(seconds == 12){

                b2pressed = 0;
                seconds = 0;
            }
        }else{
            seconds = 0;
        }





   TB0CTL &= ~TBIFG;             // Clear IRQ Flag
}




