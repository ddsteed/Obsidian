# General Programming

# PyTorch
## M1 GPU
```python
if torch.backends.mps.is_available():
    mps_device = torch.device("mps")
    G.to(mps_device)
    D.to(mps_device)
```

sometimes pytorch will show:
```
The operator 'aten::nonzero' is not currently supported on the MPS backend and will fall back to run on the CPU.
```

To solve it set the environment variable `PYTORCH_ENABLE_MPS_FALLBACK=1`

```bash
conda env config vars set PYTORCH_ENABLE_MPS_FALLBACK=1
conda activate <test-env>
```
