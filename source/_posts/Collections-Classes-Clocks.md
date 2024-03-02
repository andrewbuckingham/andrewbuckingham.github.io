---
title: Collections, Classes, Clocks
date: 2024-03-02 16:26:23
categories:
  - software-development
tags:
  - dotnet
  - c#
  - python
---

Here's a talk I did at a local tech meet-up event. This blog post is, unashamedly, *NET 8 for the Nerds*.

I was _meant_ to be speaking about the new Microsoft .NET 8 release, but decided to be rebellious and talk lots about Python instead. Partly, my excuse was that tech meet-ups are more fun if there are different technologies.

But, struggling for a better excuse, I decided to talk about new language features that are moving C# closer towards the convenience of Python. Read on to be unconvinced.

# 0. Tech we'll be using

* ***.NET 8 and C# 12:*** new language and runtime features
* ***Python:*** hugely convenient, can C# learn from it?
* ~~***Jupyter Notebooks:*** markdown and code~~ OK the talk used Jupyter but I can't do that in a blog post

I've grouped the talk into 3 parts: Collections, Classes, Clocks.

# 1. Collections

Collections are the mainstay of most applications. After all, what's ever been achieved by processing a single datapoint on its own?

Everyone has their favourite. `IEnumerable<T>` or `IList<T>` or just plain arrays. They're a bit faffy to initialise though, aren't they? It all depends on the method you're calling, and what type that expects:

{% codeblock lang:csharp %}
ProcessData(new int[] { 1, 2, 3 }); // If you're lucky.
ProcessData(new int[] { 1, 2, 3 }.ToList()); // If you're unlucky.
ProcessData(Enumerable.Empty<int>()); // Or even if you even just want to do nothing.
ProcessData(Array.Empty<int>()); // ...unless it needs an Array, in which case it's this.
{% endcodeblock %}

This is even more of a pain if you change the method signature to a more derived type. Even your empty array calls don't compile any more!

## Before we start, let's talk about Python!
Python rightly has a reputation for being quick to develop code. The syntax is really simple and doesn't have any of that unnecessary faff:

{% codeblock lang:python %}
ProcessData([1,2,3])
{% endcodeblock %}

## Can we get close to Python in .NET 8 with C# 12?

{% codeblock lang:csharp %}
// Let's try...
ProcessData([1,2,3]);

// Or even...
int[] others = [9,8];
ProcessData([1, 2, ..others, 3]);
{% endcodeblock %}

So yes we can! If you compare the Python example to that first C# 12 block above, you'll see they're literally identical.

*(in fact, when I gave this talk live, I used a Jupyter notebook and ran that "Python" code sample inside a C# .NET interpreter block, because the syntax was identical. Except I hid a semicolon on the next line. I know right? What a trickster!)*

## So what's going on?

A new language feature: collection expressions[^1]. Lots of magic under the hood. Some neat features:
* If the target type changes (e.g. a method used to take IEnumerable but now takes Array) the compiler finds the most appropriate initialiser.
* All sorts of performance optimisations internally; better than most people would hand-roll. Example[^2]

# 2. Classes

Let's create a class that processes some data and returns a calculated result.

{% codeblock lang:csharp %}
using MathNet.Numerics.Statistics;

public class Stats
{
    public Stats(IEnumerable<double> data)
    {
        _data = data;
    }

    private readonly IEnumerable<double> _data;

    public (double Average, double Deviation) Calculate()
    {
        return (_data.Average(), _data.StandardDeviation());
    }
}


var result = new Stats([1,2,3]).Calculate();

Console.WriteLine($"{result.Average}, {result.Deviation}");
{% endcodeblock %}

You'll see that this takes some data, does two calculations on it (average and stdev) and returns the result.

*(NB I have taken liberties here and enumerated the same `IEnumerable` more than once. This is usually a Bad Thing (TM) and I've done it for artistic license. Don't do it in your production systems!)*

## Can we learn much from Python?

{% codeblock lang:python %}
    import statistics

    class Stats:
        def __init__(self, data):
            self._data = data
        
        def calculate(self):
            return (statistics.mean(self._data), statistics.stdev(self._data))

    avg, dev = Stats([1, 2, 3]).calculate()
{% endcodeblock %}

So yes, Python has simplified this in a few ways:
* Eliminated the explicit declaration of the private readonly variable for `_data`
* Enables de-structuring the result directly into two variables

## Let's see what we can do with C# 12 and .NET 8

{% codeblock lang:csharp %}
using MathNet.Numerics.Statistics;

public class Stats(IEnumerable<double> data)
{
    public (double Average, double Deviation) Calculate() => (data.Average(), data.StandardDeviation());
}

var (avg, dev) = new Stats([1,2,3]).Calculate();

Console.WriteLine($"{avg}, {dev}");
{% endcodeblock %}

Hang on. That's *shorter* than the Python equivalent!

## What's going on?

Some magic syntax coming from the new C# 12 specification. We used a few recent-ish features from prior C# specifications too:

* C# 12: Primary Constructors[^3]. A bit like we see in the recent `record` class definitions we gained in C# 10. (although record classes expose the field publicly, whereas classes with primary constructors don't.)
* C# 10: Destructuring of objects into variables (since C# 7 with some restrictions)
* C# 6: Expression-bodied members

# 3. Clocks

To be fair I've dropped the Python rebellion pretence now, but I do want to talk about the most **significant** new feature to appear in .NET for many a long year.

Let's talk time.

## So you're not afraid of working with time?

Time is one of the most difficult concepts in programming. Need persuading? If you live somewhere with a summertime timezone offset, just start with that. **Can you think of any unsafe assumptions people typically make?**

{% spoiler some falsehoods about time %}

- There are always 24 hours in a day.
- February is always 28 days long.
- Any 24-hour period will always begin and end in the same day (or week, or month).
- A week always begins and ends in the same month.
- A week (or a month) always begins and ends in the same year.
- The machine that a program runs on will always be in the `GMT` time zone.
- Ok, that’s not true. But at least the time zone in which a program has to run will never change.
- Well, surely there will never be a change to the time zone in which a program hast to run in production.
- The system clock will always be set to the correct local time.
- The system clock will always be set to a time that is not wildly different from the correct local time.
- If the system clock is incorrect, it will at least always be off by a consistent number of seconds.
- The server clock and the client clock will always be set to the same time.
- The server clock and the client clock will always be set to around the same time.
- Ok, but the time on the server clock and time on the client clock would never be different by a matter of decades.
- If the server clock and the client clock are not in synch, they will at least always be out of synch by a consistent number of seconds.
- The server clock and the client clock will use the same time zone.
- The system clock will never be set to a time that is in the distant past or the far future.
- Time has no beginning and no end.
- One minute on the system clock has exactly the same duration as one minute on any other clock
- Ok, but the duration of one minute on the system clock will be pretty close to the duration of one minute on most other clocks.
- Fine, but the duration of one minute on the system clock would never be more than an hour.
- The smallest unit of time is one second.
- Ok, one millisecond.
- It will never be necessary to set the system time to any value other than the correct local time.
- Ok, testing might require setting the system time to a value other than the correct local time but it will never be necessary to do so in production.
- Time stamps will always be specified in a commonly-understood format like `1339972628` or `133997262837`.
- Time stamps will always be specified in the same format.
- Time stamps will always have the same level of precision.
- A time stamp of sufficient precision can safely be considered unique.
- A timestamp represents the time that an event actually occurred.
- Human-readable dates can be specified in universally understood formats such as `05/07/11`.
- The offsets between two time zones will remain constant.
- OK, historical oddities aside, the offsets between two time zones won’t change in the future.
- Changes in the offsets between time zones will occur with plenty of advance notice.
- Daylight saving time happens at the same time every year.
- Daylight saving time happens at the same time in every time zone.
- Daylight saving time always adjusts by an hour.
- Months have either 28, 29, 30, or 31 days.
- The day of the month always advances contiguously from `N` to either `N+1` or `1`, with no discontinuities.
- There is only one calendar system in use at one time.
- There is a leap year every year divisible by 4.
- Non leap years will never contain a leap day.
- It will be easy to calculate the duration of x number of hours and minutes from a particular point in time.
- The same month has the same number of days in it everywhere!
- Unix time is completely ignorant about anything except seconds.
- Unix time is the number of seconds since Jan 1st 1970.
- The day before Saturday is always Friday.
- Contiguous timezones are no more than an hour apart. (aka we don’t need to test what happens to the avionics when you fly over the International Date Line)
- Two timezones that differ will differ by an integer number of half hours.
- Okay, quarter hours.
- Okay, seconds, but it will be a consistent difference if we ignore `DST`.
- If you create two date objects right beside each other, they’ll represent the same time. (a fantastic Heisenbug generator)
- You can wait for the clock to reach exactly `HH:MM:SS` by sampling once a second.
- If a process runs for `n` seconds and then terminates, approximately `n` seconds will have elapsed on the system clock at the time of termination.
- Weeks start on Monday.
- Days begin in the morning.
- Holidays span an integer number of whole days.
- The weekend consists of Saturday and Sunday.
- It’s possible to establish a total ordering on timestamps that is useful outside your system.
- The local time offset (from `UTC`) will not change during office hours.
- `Thread.sleep(1000)` sleeps for 1000 milliseconds.
- `Thread.sleep(1000)` sleeps for `>=` 1000 milliseconds.
- There are 60 seconds in every minute.
- Timestamps always advance monotonically.
- `GMT` and `UTC` are the same timezone.
- Britain uses `GMT`.
- Time always goes forwards.
- The difference between the current time and one week from the current time is always `7 * 86400` seconds.
- The difference between two timestamps is an accurate measure of the time that elapsed between them.
- `24:12:34` is a invalid time.
- Every integer is a theoretical possible year.
- If you display a datetime, the displayed time has the same second part as the stored time,
- Or the same year,
- But at least the numerical difference between the displayed and stored year will be less than 2.
- If you have a date in a correct `YYYY-MM-DD` format, the year consists of four characters.
- If you merge two dates, by taking the month from the first and the day/year from the second, you get a valid date.
- But it will work, if both years are leap years
- If you take a w3c published algorithm for adding durations to dates, it will work in all cases.
- The standard library supports negative years and years above 10000.
- Time zones always differ by a whole hour.
- If you convert a timestamp with millisecond precision to a date time with second precision, you can safely ignore the millisecond fractions.
- But you can ignore the millisecond fraction, if it is less than 0.5.
- Two-digit years should be somewhere in the range 1900-2099.
- If you parse a date time, you can read the numbers character for character, without needing to backtrack.
- But if you print a date time, you can write the numbers character for character, without needing to backtrack.
- You will never have to parse a format like `---12Z` or `P12Y34M56DT78H90M12.345S`.
- There are only 24 time zones.
- Time zones are always whole hours away from UTC.
- Daylight Saving Time (DST) starts/ends on the same date everywhere.
- DST is always an advancement by 1 hour.
- Reading the client’s clock and comparing to UTC is a good way to determine their timezone.
- The software stack will/won’t try to automatically adjust for timezone/DST.
- My software is only used internally/locally, so I don’t have to worry about timezones.
- My software stack will handle it without me needing to do anything special.
- I can easily maintain a timezone list myself.
- All measurements of time on a given clock will occur within the same frame of reference.
- The fact that a date-based function works now means it will work on any date.
- Years have 365 or 366 days.
- Each calendar date is followed by the next in sequence, without skipping.
- A given date and/or time unambiguously identifies a unique moment.
- Leap years occur every 4 years.
- You can determine the time zone from the state/province.
- You can determine the time zone from the city/town.
- Time passes at the same speed on top of a mountain and at the bottom of a valley.
- One hour is as long as the next in all time systems.
- You can calculate when leap seconds will be added.
- The precision of the data type returned by a `getCurrentTime()` function is the same as the precision of that function.
- Two subsequent calls to a `getCurrentTime()` function will return distinct results.
- The second of two subsequent calls to a `getCurrentTime()` function will return a larger result.
- The software will never run on a space ship that is orbiting a black hole.
- Devices will be set to the local timezone
- Users prefer to use the local timezone

reference:[^4]
{% endspoiler %}

> ***TL;DR: If you're not Unit Testing your time calcs, your code isn't production-ready.***

What happens when we try to Unit Test our time calculations, usually? Let's create a class that does some basic time calcs.

{% codeblock lang:csharp %}
public class UntestableTimeCode()
{
    private readonly DateTimeOffset _createdOn = DateTimeOffset.Now.Date;
    public bool IsItTomorrowYet() => DateTimeOffset.Now.Date > _createdOn.Date;
}
{% endcodeblock %}

Completely fictional obviously, but this code would store today's date when you initialise it, and then you could repeatedly ask it "Is it tomorrow yet". Until it is.

The clue's in the name here: it's not really testable. But if we really *really* had to test that class, could we do it in a ~~hacky~~ creative way?

{% codeblock lang:csharp %}
using Xunit;

public class TimeTest1
{
    [Fact]
    public async Task TestIf_IsItTomorrowYet_ReturnsTrueTomorrow()
    {
        var untestable = new UntestableTimeCode();

        // Umm...?
        await Task.Delay(TimeSpan.FromHours(24));

        Assert.True(untestable.IsItTomorrowYet());
    }
}
{% endcodeblock %}

...yes. But you can see the problem here. Your unit test would take **24 hours** to run. FAIL.

## .NET 8 secret sauce

So. We need some way to modify the system clock. Or rather, de-couple our code from the system clock, and use an abstraction that we can control in a test.

{% codeblock lang:csharp %}
public class TestableTimeCode(TimeProvider timeProvider)
{
    private readonly DateTimeOffset _createdOn = timeProvider.GetUtcNow().Date;
    public bool IsItTomorrowYet() => timeProvider.GetUtcNow().Date > _createdOn.Date;
}

// In normal use, we pass the current System time provider into the constructor:
var myTimeCode = new TestableTimeCode(TimeProvider.System);
{% endcodeblock %}

OK, so we have now de-coupled from the System clock by adding a dependency. How does this make Unit Tests possible?

{% codeblock lang:csharp %}
using Microsoft.Extensions.Time.Testing;

public class TimeTest2
{
    [Fact]
    public void TestIf_IsItTomorrowYet_ReturnsTrueTomorrow()
    {
        var fakeTime = new FakeTimeProvider(startDateTime: DateTimeOffset.UtcNow.Date);
        var untestable = new TestableTimeCode(fakeTime);

    //  await Task.Delay(TimeSpan.FromHours(24)); --not any more!
        fakeTime.Advance(TimeSpan.FromHours(24));

        Assert.True(untestable.IsItTomorrowYet());
    }
}
{% endcodeblock %}

Amazing! We can artificially advance the system clock to suit our whim. 

## TimeProvider is our new best friend!

> ***Remember, take a TimeProvider dependency into all your time-based code.***

You can then use FakeTimeProvider in lots of powerful ways:

* Advance time immediately (like above)
* Timezone changes e.g. summertime
* Other countries' timezones




[^1]: https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/csharp-12.0/collection-expressions
[^2]: https://sharplab.io/#v2:CYLg1APgAgTAjAWAFBQAwAIpwCwG5lqZwB0AMgJYB2AjvkgQMyYzoDKA9gLYCmAwgDYBDAM7DkAb2TppmJlGzoAsoKqLuAFwAW7YAAoAlFJmSkMs+gAKAJ3YBjbqIAig9YIA8VdQD5dAbQC6+nRmAL5G5ujh6AAOVuQAbi7cmArWdg7Czq5uACo+WAwelN7owC6ChqbGUWFIIUA=
[^3]: https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/csharp-12.0/primary-constructors
[^4]: https://gist.github.com/timvisee/fcda9bbdff88d45cc9061606b4b923ca
