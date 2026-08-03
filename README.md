# C17 ATPG and Test Pattern Compaction using Atalanta

## Overview

This project demonstrates **Automatic Test Pattern Generation (ATPG)**, fault collapsing, fault analysis, and test pattern compaction for the **ISCAS C17 benchmark circuit** using the **Atalanta ATPG tool**.

The C17 circuit is represented in `.bench` format and consists of five primary inputs, two primary outputs, and six NAND gates.

Atalanta is used to generate a collapsed fault list, ATPG test patterns, fault-free circuit responses, and compacted test vectors. The generated patterns provide **100% fault coverage** for the targeted fault set while reducing the number of vectors required for testing.

---

## Objectives

The objectives of this project are to:

- Model the C17 benchmark circuit in `.bench` format.
- Generate a collapsed fault list using Atalanta.
- Generate ATPG test patterns for the C17 circuit.
- Obtain fault-free responses for the generated patterns.
- Determine a minimum set of test vectors for 100% fault coverage.
- Perform test pattern compaction.
- Generate test patterns for specific faults using a fault file.
- Analyze the generated `.flt` and `.test` files.

---

## C17 Benchmark Circuit

The C17 circuit used in this project has the following characteristics:

| Parameter | Value |
|---|---:|
| Circuit | C17 |
| Circuit Type | Combinational |
| Primary Inputs | 5 |
| Primary Outputs | 2 |
| Inverters | 0 |
| Logic Gates | 6 |
| Gate Type | NAND |

### Primary Inputs

```text
1, 2, 3, 6, 7
```

### Primary Outputs

```text
22, 23
```

---

## C17 Circuit Description

The circuit is represented using the Atalanta `.bench` format.

```text
# c17
# 5 inputs
# 2 outputs
# 0 inverters
# 6 gates (6 NANDs)

INPUT(1)
INPUT(2)
INPUT(3)
INPUT(6)
INPUT(7)

OUTPUT(22)
OUTPUT(23)

10 = NAND(1, 3)
11 = NAND(3, 6)
16 = NAND(2, 11)
19 = NAND(11, 7)
22 = NAND(10, 16)
23 = NAND(16, 19)
```

---

## ATPG Flow

The overall testing flow followed in this project is:

```text
              C17 Benchmark Circuit
                       │
                       ▼
                 c17.bench File
                       │
                       ▼
                    Atalanta
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
     Fault List    Test Patterns   Fault-Free
                                  Responses
          │            │
          ▼            ▼
   Fault Collapsing   ATPG
          │            │
          └──────┬─────┘
                 ▼
         Pattern Compaction
                 │
                 ▼
       Reduced Test Pattern Set
                 │
                 ▼
          Fault Coverage
```

---

## Running Atalanta

### 1. Compile Atalanta

Navigate to the Atalanta source directory:

```bash
cd ~cad/atalanta
make
```

This generates the Atalanta executable.

The executable can then be copied to the required `bin` directory.

```bash
cp atalanta ~cad/bin
```

---

### 2. Configure the Environment

The required environment variables were configured using `csh`.

```csh
csh

set path = (/home/student/Desktop/25MVD1076/bin)

setenv ATLANTA_MAN ./atalanta
setenv FSIM_MAN ./fsim
setenv HOPE_MAN ./hope
```

> The paths above correspond to the laboratory environment used for this implementation and may need to be changed for another installation.

---

## Atalanta Commands

### Generate Collapsed Fault List, Test Patterns and Fault-Free Responses

```bash
atalanta -t c17.test -D 2 c17.bench
```

This command was used to generate the test patterns together with the fault-free responses and collapsed fault information.

---

### Generate Minimum Test Vectors for 100% Fault Coverage

```bash
atalanta -N c17.test c17.bench
```

This was used to obtain a reduced number of test vectors while maintaining **100% fault coverage**.

---

### Test Pattern Compaction

```bash
atalanta -t c17.test c17.bench
```

The generated test patterns were compacted to reduce the number of vectors required for effective testing.

---

### ATPG for Specific Faults

```bash
atalanta -f c17.flt c17.bench
```

This command generates test patterns targeting the faults specified in the `c17.flt` fault file.

---

## Collapsed Fault List

The generated `c17.flt` file contains the following faults:

```text
7 /1
16->23 /1
11->19 /1
2 /0
6 /0
10->22 /0
```

The `/0` and `/1` notation represents the stuck-at value associated with the corresponding fault.

The collapsed fault list reduces the number of faults that need to be explicitly targeted during ATPG.

---

## Generated Test Patterns

One of the generated `c17.test` files contained the following test patterns and corresponding fault-free responses:

| Pattern | Input Vector | Fault-Free Response |
|---:|:---:|:---:|
| 1 | `00000` | `00` |
| 2 | `11111` | `10` |
| 3 | `01000` | `11` |

Thus, a compact set of **three input vectors** was generated in this output.

---

## ATPG for Individual Faults

Atalanta was also used to generate patterns for individual faults.

For example:

```text
Fault: 7 /1
Pattern: x00x0
Response: 00
```

Another example:

```text
Fault: 16->23 /1

Pattern 1: x10x0
Response : 11

Pattern 2: x1100
Response : 11
```

Here, `x` represents a **don't-care input value**, meaning that the corresponding input does not need to be fixed to either logic `0` or logic `1` for that test condition.

---

## Example Fault-Specific Patterns

Some of the generated fault-specific ATPG results are:

| Target Fault | Example Test Pattern | Response |
|---|---|---|
| `7 /1` | `x00x0` | `00` |
| `11->19 /1` | `xx111` | `x0` |
| `16->23 /1` | `x10x0` | `11` |
| `19 /1` | `x00x1` | `01` |
| `6 /1` | `0110x` | `1x` |
| `2 /1` | `000xx` | `0x` |
| `16 /0` | `00xxx` | `0x` |
| `3 /1` | `100xx` | `0x` |
| `3 /0` | `101xx` | `1x` |
| `1 /1` | `001xx` | `0x` |
| `16->22 /1` | `010xx` | `11` |
| `10 /1` | `101xx` | `1x` |

These patterns demonstrate how ATPG determines the input conditions required to detect different faults in the C17 circuit.

---

## Test Pattern Compaction

ATPG can initially generate multiple patterns capable of detecting different faults.

However, a single test vector may detect multiple faults.

Therefore, test pattern compaction is used to eliminate unnecessary vectors and retain a smaller test set capable of maintaining the required fault coverage.

```text
Generated ATPG Patterns
          │
          ▼
  Fault Detection Analysis
          │
          ▼
  Identify Redundant Patterns
          │
          ▼
    Pattern Compaction
          │
          ▼
   Reduced Test Pattern Set
          │
          ▼
     100% Fault Coverage
```

The results showed that the number of required input vectors could be reduced while still achieving complete fault coverage for the targeted fault set.

---

## Results

The C17 benchmark circuit was successfully analyzed using Atalanta.

The experiment successfully generated:

- Collapsed fault list
- ATPG test patterns
- Fault-free responses
- Fault-specific test patterns
- Compacted test vectors

The generated test patterns achieved:

### **100% Fault Coverage**

The results demonstrate that a reduced set of test vectors is sufficient to detect the targeted faults in the C17 circuit.

---

## Output Files

The main files generated and used in this project are:

| File | Purpose |
|---|---|
| `c17.bench` | C17 circuit description |
| `c17.flt` | Fault list |
| `c17.test` | Generated ATPG test patterns and responses |

---

## Tools and Technologies

| Tool / Format | Purpose |
|---|---|
| Atalanta | Automatic Test Pattern Generation |
| `.bench` | C17 circuit representation |
| Linux / C Shell | Tool execution environment |
| ATPG | Test pattern generation |
| Fault Simulation | Evaluation of fault detection |
| Fault Collapsing | Reduction of fault set |
| Pattern Compaction | Reduction of test vectors |

---

## Repository Structure

```text
C17-ATPG-Using-Atalanta/
│
├── README.md
│
├── circuit/
│   └── c17.bench
│
├── faults/
│   └── c17.flt
│
├── patterns/
│   ├── c17.test
│   └── fault_specific_patterns.txt
│
└── results/
    ├── atalanta_output.png
    └── output_verification.png
```

---

## Key Learnings

Through this project, I gained practical experience with:

- Automatic Test Pattern Generation (ATPG)
- ISCAS C17 benchmark circuit
- Single stuck-at fault testing
- Stem and branch fault representation
- Fault collapsing
- Fault-specific pattern generation
- Don't-care values in ATPG patterns
- Fault-free response generation
- Test pattern compaction
- Fault coverage analysis
- Atalanta command-line ATPG workflow

---

## Conclusion

The **C17 benchmark circuit** was successfully tested using the **Atalanta ATPG tool**.

Atalanta generated the collapsed fault list, test vectors, fault-free responses, and fault-specific ATPG patterns. Test pattern compaction reduced the number of vectors required for effective testing while maintaining **100% fault coverage**.

This project demonstrates important Design-for-Test concepts including **fault modeling, fault collapsing, ATPG, fault simulation, test pattern generation, and test pattern compaction**.
