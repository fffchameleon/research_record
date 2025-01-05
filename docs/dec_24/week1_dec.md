# Dec. week 1
!!! Note
    Date: 12/1 (Sun.) ~ 12/7 (Sat.)
- [TF slide link](https://drive.google.com/file/d/1Ny7JiG6wcpGye03_qXFuy-XCia-SdsIy/view?usp=drive_link){target="_blank"}
## TF progress report
- [x] summary proposal comment
    - [x] schedule progress
- [x] initialization process profiling

## Summary proposal comment
- 口委所提出的問題很多都是針對實驗的部分
    - 我的方法 (MoM method) 的使用情境為何？
        - 因當問題為 evaluation 較昂貴時，使用 MoM 的效果就並不顯著
    - 做實驗的感覺是一直增加 population size 直到 MoM 的方法勝出
    - 是否有 margin effect？（即當 population size 增加時 performance 沒有增加
    - 在做實驗時沒有定義好困難的問題（是 search space 複雜？還是 dimension 很高？ 還是 evaluation很貴？）
    - 我目前的初步實驗的 dimension 其實不高 (100-D 的 Schwefel function)
- 在演化後期，parent 會比 offspring 還要強，所以其實 sorting 時不需要用到 global sorting，有可能把 parent sorting 好就好
- 我的方法的時間複雜度分析是考慮 worst case
    - 在 sorting 的部分需要考慮不同情境的 complexity 去確認我的方法是否 always 獲勝
    - 榮佐學長：insertion sort 在當資料都差不多排好的時候很有效
        - My notes: 但 insertion sort 並不適合 deploy 到 GPU
- 有理論方面的貢獻，但在實務上很缺乏
- Steady state experiment

---

## Initialization process
!!! Previous_implementation
    - Using a thread to handle an individual
        - Issue: 
            - Non coalesced read/write in global
            - When in high-dimension, the share memory size will block the threads to be launched

### Profiling the result
- 之前的 implementation 是 memory-bound: (memory throughput/SM throughput): (79.01/6.67 %)
- 目前的方法所算出來的理論 active warps per SM 為 3，相較 RTX 4080 的 maximum active warps per SM 為 48 差相當多
- This week goals: Using a thread to handle specific number of genes
- Using streaming for each row (DIMENSION) to launch kernel
    - Issue: kernel launch has the cost
    - Goal: launch once and do best