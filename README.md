# ThresholdEventApp
A simple C# event-based counter app
using System;

public class ThresholdReachedEventArgs : EventArgs
{
    public int Threshold { get; set; }
    public DateTime TimeReached { get; set; }
}

public class Counter
{
    private int total;
    private readonly int minThreshold;
    private readonly int maxThreshold;

    public event EventHandler<ThresholdReachedEventArgs> ThresholdReached;

    public Counter(int minThreshold, int maxThreshold)
    {
        this.minThreshold = minThreshold;
        this.maxThreshold = maxThreshold;
        total = 0;
    }

    public void Add(int x)
    {
        if (total <= maxThreshold)
        {
            total += x;
            if (total == maxThreshold)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("🚫 حد پایین (0) رسیدی! دیگه کمتر نمی‌تونی بری. ");
                Console.ResetColor();

                Console.WriteLine("برای ادامه Enter بزن...");
                Console.ReadLine();
            }
            CheckThreshold();
        }
        else
        {
            Console.WriteLine($"⚠️ حد بالایی ({maxThreshold}) رسیدی. نمی‌تونی بیشتر اضافه کنی!");
        }

        Console.WriteLine($"🔊 Volume: {total}");
    }

    public void Minus(int y)
    {
        if (total >= minThreshold)
        {
            total -= y;
            if (total == 0)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("🚫 حد پایین (0) رسیدی! دیگه کمتر نمی‌تونی بری. ");
                Console.ResetColor();

                Console.WriteLine("برای ادامه Enter بزن...");
                Console.ReadLine();
                return;
            }
            CheckThreshold();
        }
        else
        {
            Console.WriteLine($"⚠️ حد پایینی ({minThreshold}) رسیدی. نمی‌تونی کمترش کنی!");
        }

        Console.WriteLine($"🔊 Volume: {total}");
    }

    private void CheckThreshold()
    {
        if (total == maxThreshold || total == minThreshold)
        {
            ThresholdReached?.Invoke(this, new ThresholdReachedEventArgs
            {
                Threshold = total,
                TimeReached = DateTime.Now
            });
        }
    }
}

public class Program
{
    static void Main()
    {
        // ✅ اضافه کردن پشتیبانی از فارسی
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        Console.InputEncoding = System.Text.Encoding.UTF8;

        Counter c = new Counter(0, 50);
        c.ThresholdReached += OnThresholdReached;

        Console.WriteLine("Volume : 🎚 کنترل حجم صدا");
        Console.WriteLine(" Volom up Volom Down || کلید 'a' برای افزایش، 'b' برای کاهش، 'q' برای خروج");

        while (true)
        {
            var key = Console.ReadKey(true).KeyChar;

            if (key == 'q')
                break;

            if (key == 'a')
                c.Add(1);
            else if (key == 'b')
                c.Minus(1);
        }
    }

    private static void OnThresholdReached(object sender, ThresholdReachedEventArgs e)
    {
        Console.WriteLine($"🚫 Threshold {e.Threshold} reached at {e.TimeReached:T}!");
    }
    
    
}
