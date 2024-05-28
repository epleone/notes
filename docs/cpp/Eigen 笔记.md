# Eigen 笔记


## preface

[libeigen / eigen · GitLab](https://gitlab.com/libeigen/eigen)

[Site Unreachable](http://eigen.tuxfamily.org/)



## 稀疏矩阵求解


``` c++
/*
*  使用LU分解求解方程
*/

#include <Eigen/Sparse>
#include <Eigen/Dense>

using namespace Eigen;


// 构建稀疏矩阵
SparseMatrix<float> D(maskSize, maskSize);


// 插入非零元素， 这种做法耗时巨大
for (int k = 0; k < Idx_i.size(); ++k) {
	D.insert(Idx_i[k], Idx_j[k]) = s[k];
}

// 压缩稀疏矩阵, 稀疏矩阵`D`被压缩，以节省内存和提高运算效率。
D.makeCompressed();


// 创建SparseLU对象，使用`compute()`方法对稀疏矩阵`D`进行LU分解，以便后续求解。
Eigen::SparseLU<Eigen::SparseMatrix<float> > solver;
solver.compute(D);

// 检查分解是否成功
if (solver.info() != Eigen::Success) {
	print_log("Decomposition failed!");
	return -1;
}


// 构建右侧向量, VectorXf表示float,  VectorXd表示double
Eigen::VectorXf rightSideVec = Eigen::VectorXf::Map(rightSide.data(), rightSide.size());

// 求解线性系统
Eigen::VectorXf sol = solver.solve(rightSideVec);

// 检查求解是否成功
if (solver.info() != Eigen::Success) {
	print_log("Solving failed!");
	return -1;
}
```


除了使用SparseLU进行稀疏矩阵的分解之外，Eigen库还提供了其他一些方法来解决稀疏矩阵线性系统。以下是其中一些常用的方法：
1. **BiCGSTAB（BiConjugate Gradient STABilized）**：这是一种迭代方法，通常适用于对称和非对称稀疏矩阵的求解。它是BiCG方法的改进版本，能够更快地收敛于解。它的优点之一是不需要对整个矩阵进行分解。
2. **ConjugateGradient**：共轭梯度法也是一种迭代方法，通常用于对称正定稀疏矩阵的求解。它通过最小化残差的二次型来逐步逼近解。
3. **SparseQR**：这是用于求解稀疏矩阵的QR分解的方法。它适用于稀疏非正定和不对称矩阵。
4. **Cholmod**：这是一个基于CHOLMOD库的稀疏Cholesky分解求解器。适用于对称正定稀疏矩阵的求解。
5. **GMRES（Generalized Minimal RESidual）**：这也是一种迭代方法，通常用于非对称稀疏矩阵的求解。它能够在每一步中寻找一个最小化残差的近似解。

这些方法各有优劣，适用于不同类型的稀疏矩阵和求解需求。选择方法通常取决于矩阵的特性（对称/非对称、正定/不定等）以及性能需求（速度、精度等）。


``` c++

// 使用QR分解求解方程

// 创建SparseQR求解器
Eigen::SparseQR<Eigen::SparseMatrix<float>, Eigen::COLAMDOrdering<int>> solver;
solver.compute(D);

// 检查分解是否成功
if (solver.info() != Eigen::Success) {
    print_log("Decomposition failed!");
    return -1;
}

// 构建右侧向量
Eigen::VectorXf rightSideVec = Eigen::VectorXf::Map(rightSide.data(), rightSide.size());

// 求解线性系统
Eigen::VectorXf sol = solver.solve(rightSideVec);

// 再次检查解是否成功
if (solver.info() != Eigen::Success) {
    print_log("Solving failed!");
    return -1;
}
```




``` c++

// 构建稀疏矩阵
SparseMatrix<float> D(maskSize, maskSize);


// 插入非零元素

// 方法一 这种做法耗时巨大
for (int k = 0; k < Idx_i.size(); ++k) {
	D.insert(Idx_i[k], Idx_j[k]) = s[k];
}
D.makeCompressed();


// 方法二， 时间性能快太多
std::vector<Eigen::Triplet<float>> triplets;
triplets.reserve(Idx_i.size()); // 预分配三元组向量的内存

for (int k = 0; k < Idx_i.size(); ++k) {
    triplets.push_back(Eigen::Triplet<float>(Idx_i[k], Idx_j[k], s[k]));
}

D.setFromTriplets(triplets.begin(), triplets.end());
D.makeCompressed(); 

```