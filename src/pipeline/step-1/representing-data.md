# Representing data

## Binary
You may be familiar with the notion that computers internally handle data as
sequences of zeros and ones. Indeed, a "binary" representation of numbers
(and more generally, any kind of data) turns out to be a natural choice for
computers and electrical circuitry. So how does it work?

Consider the number 2023. When you read this number, you implicitly understand
it using the base-10 representation we all learned in grade school. 2023 is made
up of 2 thousands, 0 hundreds, 2 tens, and 3 ones. Or:

> 2023 = __2__ × 10<sup>3</sup> + __0__ × 10<sup>2</sup> + __2__ × 10<sup>1</sup> + __3__ × 10<sup>0</sup>.

This common base-10 (or "decimal") representation of numbers likely developed because of
the 10 fingers on human hands, but there is really nothing special about 10. For example,
it is also true that:[^1]

> 2023 = __1__ × 2<sup>10</sup> + __1__ × 2<sup>9</sup> + __1__ × 2<sup>8</sup> + __1__ × 2<sup>7</sup> + __1__ × 2<sup>6</sup> + __1__ × 2<sup>5</sup> + __0__ × 2<sup>4</sup> + __0__ × 2<sup>3</sup> + __1__ × 2<sup>2</sup> + __1__ × 2<sup>1</sup> + __1__ × 2<sup>0</sup>.

Working in a base-2 (or "binary") representation, where every digit must be
either 0 or 1, we can see that 2023 can be written as 11111100111. To avoid
confusion between bases, we typically prepend the prefix "0b" to numbers writen
in binary. And so, 2023 = 0b11111100111.

Binary gives us a way to "encode" signals sent on wires into something more
familiar—numbers. A signal with a voltage profile of high-high-high-low, for
example,  maps to 0b1110 in binary, or 14 in decimal. Nevertheless, binary is
difficult for humans to work with directly. We need a better representation for
raw data[^2].

> ### Terminology
> Just as we refer to each character in a decimal number as a digit, we also define the
> term *bit* to mean "<b>b</b>inary dig<b>it</b>". The number 0b10101011 has 8 bits.
>
> Binary numbers can grow quite long, and so *bytes* (where 1 byte = 8 bits) are more
> commmonly used as a unit when speaking of binary data.


## Hexadecimal
Hexadecimal is a base-16 numbering system. Like binary, it is more useful for representing
raw data than decimal. But unlike binary, hexadecimal tends to be much easier to read at
a glance. Continuing the previous example, we may write:

> 2023 = __7__ × 16<sup>2</sup> + __14__ × 16<sup>1</sup> + __7__ × 16<sup>0</sup>

Here we run into trouble with the numerals we have at our disposal. The above coefficients
would suggest that the base-16 representation of 2023 is something like 7|14|7. Since there
are no natural single-character glyphs for numbers above 9, the common practice is to continue
with the alphabet (10 → A, 11 → B, 12 → C, etc.). Therefore, the hexadecimal representation of
2023 becomes 0x7E7 (where the prefix "0x" denotes hexadecimal).


| decimal | binary | hexadecimal |
| :-----: | :----: | :---------: |
|    00   | 0b0000 |     0x0     |
|    01   | 0b0001 |     0x1     |
|    02   | 0b0010 |     0x2     |
|    03   | 0b0011 |     0x3     |
|    04   | 0b0100 |     0x4     |
|    05   | 0b0101 |     0x5     |
|    06   | 0b0110 |     0x6     |
|    07   | 0b0111 |     0x7     |
|    08   | 0b1000 |     0x8     |
|    09   | 0b1001 |     0x9     |
|    10   | 0b1010 |     0xA     |
|    11   | 0b1011 |     0xB     |
|    12   | 0b1100 |     0xC     |
|    13   | 0b1101 |     0xD     |
|    14   | 0b1110 |     0xE     |
|    15   | 0b1111 |     0xF     |
<div style="text-align: center"><i>The first 16 natural numbers in decimal, binary, and hexadecimal</i></div>

As you'll see in the next chapter, both binary and hexadecimal are useful ways of interpreting
messages sent on the CAN bus.

---

[^1]: Don't worry about how to come up with this expression for now. It's not terribly difficult,
but for this training you will only need to be familiar with the concept and the appropriate
Python functions to call.

[^2]: "Raw data" generally refers to any information that is not meant to be read or written
by humans. CAN frames, as we will see later, are one such example. It is also worth noting
that raw data is often referred to as *binary* data to emphasize that it is best thought of
as bytes and not as text. You may also hear executable files called *binaries* for a
similar reason.
