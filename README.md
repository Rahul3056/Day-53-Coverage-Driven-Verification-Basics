### **Day 53: Coverage-Driven Verification – Basics**

---

### **Objective:**

Understand the basics of **coverage-driven verification (CDV)** in SystemVerilog, a technique used to measure and improve how well your tests **exercise the design**.

CDV helps ensure that all relevant functionality and corner cases of a design have been tested.

---

## ✅ 1. What is Coverage?

**Coverage** in verification measures how much of the **design or verification intent** has been exercised by your testcases. There are different types:

* **Code Coverage** (handled by simulator): statement, branch, toggle
* **Functional Coverage** (user-defined in SystemVerilog): based on variables, transactions, conditions

SystemVerilog supports **functional coverage** via `covergroup`.

---

## ✅ 2. Why Functional Coverage?

Functional coverage lets you track whether certain **stimulus scenarios**, values, or behaviors have occurred. It helps answer:

* Have all address types been tested?
* Have both read/write operations occurred?
* Were all combinations of data and control tested?

---

## ✅ 3. Basic Components of Functional Coverage

| Component    | Description                               |
| ------------ | ----------------------------------------- |
| `covergroup` | Defines what to track                     |
| `coverpoint` | Tracks values of a variable               |
| `cross`      | Tracks combinations of multiple variables |

---

## ✅ 4. Syntax: Declaring a Covergroup

```systemverilog
covergroup cg @(posedge clk);
    coverpoint addr;
    coverpoint rw;
    cross addr, rw;
endgroup
```

---

## ✅ 5. Example: Simple Functional Coverage

```systemverilog
class Packet;

    rand bit [3:0] addr;
    rand bit rw;  // 0: read, 1: write

    // Covergroup inside class
    covergroup cov @(posedge clk);
        coverpoint addr;
        coverpoint rw;
        cross addr, rw;
    endgroup

    function new();
        cov = new();
    endfunction

endclass
```

---

### **Testbench:**

```systemverilog
module tb;

    logic clk;
    always #5 clk = ~clk;

    Packet pkt;

    initial begin
        clk = 0;
        pkt = new();

        repeat (20) begin
            pkt.randomize();
            @(posedge clk);
            pkt.cov.sample();  // sample coverage after every transaction
        end

        $display("Simulation complete. Check coverage report.");
        $finish;
    end

endmodule
```

---

## ✅ 6. Simulation Output

You won't see coverage in the console output. Instead, simulators like **VCS**, **Questa**, or **Xcelium** generate a **coverage report** showing:

* Which values of `addr` and `rw` were hit
* Which combinations (cross) were missed
* % coverage for each coverpoint and cross

---

## ✅ 7. Important Notes

* Coverage must be **sampled** using `.sample()` unless auto-sampled
* Coverage is **declarative**—it doesn't drive stimulus
* You can **filter or disable** unwanted bins using constraints or `ignore_bins`
* Functional coverage helps **close coverage holes** in CDV

---

## ✅ Summary

| Concept      | Purpose                                        |
| ------------ | ---------------------------------------------- |
| `covergroup` | Defines a unit of coverage collection          |
| `coverpoint` | Tracks values of a variable                    |
| `cross`      | Tracks combinations of two or more coverpoints |
| `.sample()`  | Samples the current values during simulation   |
| CDV Benefit  | Validates stimulus effectiveness               |

