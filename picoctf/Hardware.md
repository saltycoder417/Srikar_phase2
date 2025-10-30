# 1. IQ test: 

let your input x = 30478191278.

wrap your answer with nite{ } for the flag.

As an example, entering x = 34359738368 gives (y0, ..., y11), so the flag would be nite{010000000011}.

They gave a Logic gate circuit and asked us to solve it.

![Circuit diagram](images/iqtest.png)

## Solution:

- Since we have a logic gate circuit, which can only take 0 or 1, and they input given is in decimal, we first have to convert x to binary. We also should make sure it has 36 bits since we have 36 inputs. 

```
30478191278= 011100011010010010100100101010101110
```
- After we convert it to binary, we have to take x0 as the left most digit, and so on so x0=0, x1=1, x2=1, ..... Then we have to solve the logic gates either manually since they gave basic gates or we can use a code. I solved it manually
- If we observe carefully, each output is given by three inputs: y0 is given by x0, x1, x2, and so on, so we have to do it 12 times.
- After solving it, we will get the final output as 100010011000. 

## Flag:

```
nite{100010011000}
```

## Concepts learnt:

- I learnt how to solve complex circuits of logic gates. 

## Notes:

- It is a very dense and complex circuit, so we have to be careful while solving as a small mistake can be difficult to identify.  

## Resources:

- https://www.rapidtables.com/convert/number/decimal-to-binary.html

***

