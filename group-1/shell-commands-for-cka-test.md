# Shell Commands for CKA Test

#### 1. Alias for fast typing

```
alias k=kubectl
export do='--dry-run=client -o yaml'
```

#### 2. Output Options

```
k get pods | cat -n #output with line number
k get pods | grep Image: #find output of "Image:"
```
