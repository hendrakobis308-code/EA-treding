//+------------------------------------------------------------------+
//|                 EA-treding SND + SNR Indicator                   |
//|                   Multi Timeframe Signal (MT5)                   |
//+------------------------------------------------------------------+
#property copyright "EA-treding"
#property link      "https://github.com/hendrakobis308-code/EA-treding"
#property version   "1.0"
#property indicator_separate_window
#property indicator_buffers 4
#property indicator_plots   2

//--- plot buy/sell signals
#property indicator_label1  "Buy Signal"
#property indicator_type1   DRAW_ARROW
#property indicator_color1  clrLime
#property indicator_width1  2

#property indicator_label2  "Sell Signal"
#property indicator_type2   DRAW_ARROW
#property indicator_color2  clrRed
#property indicator_width2  2

//--- buffers
double BuyBuffer[];
double SellBuffer[];

//--- settings
input ENUM_TIMEFRAMES TimeFrame = PERIOD_H1;   // Timeframe analisa
input int LookBackBars          = 500;         // Jumlah bar diperiksa
input int ZoneStrength          = 3;           // Minimal sentuhan zona
input double Sensitivity        = 0.5;         // Sensitivitas S/R

//+------------------------------------------------------------------+
//| Custom indicator initialization                                  |
//+------------------------------------------------------------------+
int OnInit()
  {
   SetIndexBuffer(0, BuyBuffer, INDICATOR_DATA);
   SetIndexArrow(0, 233); // Panah ke atas
   SetIndexBuffer(1, SellBuffer, INDICATOR_DATA);
   SetIndexArrow(1, 234); // Panah ke bawah
   ArrayInitialize(BuyBuffer, EMPTY_VALUE);
   ArrayInitialize(SellBuffer, EMPTY_VALUE);
   return(INIT_SUCCEEDED);
  }

//+------------------------------------------------------------------+
//| Custom indicator iteration function                              |
//+------------------------------------------------------------------+
int OnCalculate(const int rates_total,
                const int prev_calculated,
                const datetime &time[],
                const double &open[],
                const double &high[],
                const double &low[],
                const double &close[],
                const long &tick_volume[],
                const long &volume[],
                const int &spread[])
  {
   int start = rates_total - LookBackBars;
   if(start < 0) start = 0;

   for(int i=start; i<rates_total-1; i++)
     {
      // Contoh deteksi zona support
      if(close[i] <= iLow(NULL, TimeFrame, i) + Sensitivity*Point)
        {
         BuyBuffer[i] = low[i] - (10 * Point);
        }

      // Contoh deteksi zona resistance
      if(close[i] >= iHigh(NULL, TimeFrame, i) - Sensitivity*Point)
        {
         SellBuffer[i] = high[i] + (10 * Point);
        }
     }

   return(rates_total);
  }
//+------------------------------------------------------------------+
