# RV-Sparse Coding Challenge

This repository contains a C solution for the RV-Sparse coding challenge. The task is to implement `sparse_multiply`, a function that:

- scans a row-major dense matrix `A`
- extracts its non-zero entries into CSR (Compressed Sparse Row) format
- computes the matrix-vector product `y = A * x`
- writes all outputs into caller-provided buffers

The main constraint from the challenge brief is that the function must perform zero dynamic memory allocation. All required memory is pre-allocated by the caller.

## Files

- `docs/RV-Sparse_ Coding Challenge.pdf`: original challenge brief
- `challenge.c`: challenge implementation and test harness
- `readme.md`: project documentation

## Function Behavior

The implemented function is:

```c
void sparse_multiply(
    int rows,
    int cols,
    const double* A,
    const double* x,
    int* out_nnz,
    double* values,
    int* col_indices,
    int* row_ptrs,
    double* y
);
```

Inputs:

- `rows`, `cols`: matrix dimensions
- `A`: dense row-major matrix of size `rows * cols`
- `x`: dense input vector of size `cols`

Outputs:

- `out_nnz`: total number of non-zero entries found in `A`
- `values`: CSR non-zero values
- `col_indices`: column index for each non-zero value
- `row_ptrs`: CSR row offsets, with length `rows + 1`
- `y`: dense output vector of size `rows`

CSR layout produced by this implementation:

- `row_ptrs[0] = 0`
- `row_ptrs[i + 1]` stores the exclusive end offset of row `i`
- non-zero elements are emitted in row-major order

## Implementation Summary

The solution performs a single pass over the dense matrix:

1. Initialize `row_ptrs[0] = 0`.
2. For each row, initialize `y[i] = 0`.
3. For each element `A[i * cols + j]`:
   - if the value is non-zero, append it to `values`
   - store `j` in `col_indices`
   - accumulate `value * x[j]` into `y[i]`
4. After each row, write the current non-zero count into `row_ptrs[i + 1]`.
5. Store the final non-zero count in `*out_nnz`.

This satisfies the challenge constraint because the function does not allocate memory internally.

## Build And Run


```sh
gcc -o run challenge.c -lm
./run
```

## Verification

The included test harness:

- generates 100 random matrices
- varies matrix dimensions and sparsity
- compares the function output against a dense reference multiplication
- validates using a mixed absolute/relative tolerance

Verification result:

```text
All tests passed! (100/100 iterations passed)
```

