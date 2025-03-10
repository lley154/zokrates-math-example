# Zokrates Math Example
Open a terminal window
```
$ mkdir ~/zokrates
$ cd ~/zokrates
$ docker run -v /path-to-zokrates-directory/:/workspace -it zokrates/zokrates /bin/bash
$ cd /workspaces
$ mkdir math
$ cd math
```
In an new terminal window, in the ~/zokrates/math directory create a file with the following content and save it a math.zok. We will prove knowledge of that we know 2 numbers with the both the product and the sum match the known outputs.
```
def main(private field a, private field b) -> (field, field) {
    field c = a * b;
    field d = a + b;
    return (c, d);
}
```

- The keyword ```field``` is the basic type we use
- The keyword ```private``` signals that we do not want to reveal this input, but still prove that we know its value

Then execute the following commands inside the docker container.
### Compile
```
$ zokrates compile -i math.zok
$ ls -l
total 16
-rw-r--r-- 1 zokrates zokrates 260 Mar 10 04:27 abi.json
-rw-r--r-- 1 zokrates zokrates 645 Mar 10 04:27 out
-rw-r--r-- 1 zokrates zokrates 256 Mar 10 04:27 out.r1cs
-rw-rw-r-- 1 zokrates zokrates  75 Mar 10 04:26 math.zok

```
- The ```abi.json``` file describes the structure of the outputs from your zero-knowledge proof circuit
- The ```out``` file is the compiled binary representation of your circuit and contains the arithmetic circuit in a format that ZoKrates can execute
- The ```out.r1cs``` file (Rank-1 Constraint System) is a mathematical representation of your program's constraints. It is used to convert your program into a form that can be proven using zk-SNARKs.

### zkSNARK Setup
```
$ zokrates setup
$ ls -l
total 24
-rw-r--r-- 1 zokrates zokrates  260 Mar 10 04:27 abi.json
-rw-r--r-- 1 zokrates zokrates  645 Mar 10 04:27 out
-rw-r--r-- 1 zokrates zokrates  256 Mar 10 04:27 out.r1cs
-rw-r--r-- 1 zokrates zokrates 1776 Mar 10 04:41 proving.key
-rw-rw-r-- 1 zokrates zokrates   75 Mar 10 04:26 math.zok
-rw-r--r-- 1 zokrates zokrates 1589 Mar 10 04:41 verification.key
```
- The ```proving.key``` is has the trusted setup parameters and used by the prover to generate proofs
- The ```verification.key``` contains public parameters needed to verify proofs and used by verifiers

### Execute the program
```
$ zokrates compute-witness -a 3 4 --verbose
Computing witness...

Witness: 
["12","7"]

$ ls -l
total 32
-rw-r--r-- 1 zokrates zokrates  260 Mar 10 04:27 abi.json
-rw-r--r-- 1 zokrates zokrates  645 Mar 10 04:27 out
-rw-r--r-- 1 zokrates zokrates  256 Mar 10 04:27 out.r1cs
-rw-r--r-- 1 zokrates zokrates  172 Mar 10 04:46 out.wtns
-rw-r--r-- 1 zokrates zokrates 1776 Mar 10 04:41 proving.key
-rw-rw-r-- 1 zokrates zokrates   75 Mar 10 04:26 math.zok
-rw-r--r-- 1 zokrates zokrates 1589 Mar 10 04:41 verification.key
-rw-r--r-- 1 zokrates zokrates  128 Mar 10 04:46 witness
```
- The ```out.wtns``` is a binary file that contains all circuit variables (private and public), intermediate values computed during circuit execution and the actual values that satisfy the R1CS constraints
- the ```witness``` file is the public outputs of the computation


### Generate a proof of computation
```
$ zokrates generate-proof
Generating proof...
Proof written to 'proof.json'
```


### Verify
```
$ zokrates verify
Performing verification...
PASSED
```

