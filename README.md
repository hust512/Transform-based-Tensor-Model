## TensorLet: A C++ library for popular tensor decompositions

* [TensorLet](#readme)
	* [Installation](#Installation)
	* [User guide](#user-guide)
	* [API Reference](#api-reference)
	* [Class List](#class-list)
	* [References](#references)
	
## Installation
<details>	
<summary> Pre-requisite </summary>
Users need the following packages:   
	
1. Basic matrix library, Eigen: https://eigen.tuxfamily.org/dox/   

2. Intel Math Kernel Library (MKL): https://software.intel.com/en-us/mkl  

3. (Optional) OpenMP: https://www.openmp.org/  
We use MKL as basic matrix library for high performance and test our code on Ubuntu.  

Intel Math Kernel Library (Intel MKL) is a library which is hand-optimized specifically for Intel processors. Core math functions include BLAS, LAPACK, ScaLAPACK, sparse solvers, fast Fourier transforms, and vector math.    
</details>

<details>	
<summary> Instructions </summary>
We suggest users use TensorLet on Ubuntu and you can refer to the installation instructions in Installation folder.   

You need to add them to specific paths according to your CMakeLists.txt file. For example, you can link MKL in  CMakeLists.txt file,     
"include_directories(/opt/intel/mkl/include)  
link_directories(/opt/intel/mkl/lib/intel64)  
link_libraries(libmkl_core.a libmkl_blas95_ilp64.a libmkl_rt.so)"  

<!--
3. cmake version 3.12 or greater: https://cmake.org/    

4. Fastest Fourier Transform in the West (FFTW): http://www.fftw.org/    
-->
[//]: # (This may be the most platform independent comment)  

</details>

## User guide
In TensorLet, all third order tensors are objects of the Tensor3D template class. You can refer to Class list for more details.

<details>	
<summary> CANDECOMP/PARAFAC decomposition </summary>

CP decomposition via alternating least squares (ALS), which is realized in cp_als.cpp.    

The decomposition components of CP is defined as:  
>template\<class type\>  
>class cp_decomposition{  
>&emsp;&emsp;    Mat\<type\> factor[3];  
>};  
where, Mat\<type\> is dense matrix class provided by the third party library Eigen/MKL;      
The template parameter <type> represents the data type of tensor and be <double> and <float>;  
The factor is the matrix list of the corresponding CP decomposition.   

You can call cp_als function like:   

        cp_decomposition<double> A = cp_decomposition(Tensor3D<double> &tensor, int rank, int max_iter，double tol);    

where Tensor3D\<type\> represents the third-order tensor class.
</details>

<details>	
<summary> Tucker decomposition </summary>

Tucker decomposition via Higher Order SVD (HOSVD), which is realized in tucker_hosvd.cpp.  
Tucker decomposition via Higher Order Orthogonal Iteration (HOOI), which is realized in tucker_hooi.cpp.    

The decomposition components of tucker is defined as:  
>template\<class type\>    
>class tucker_decomposition{  
>&emsp;&emsp;  Tensor3D\<type\> core, Mat\<type\> factor[3];   
>};  
where factor is the matrix list of the corresponding Tucker decomposition.   

You can call hosvd function like: 

        tucker_decomposition<double> A = tucker_hosvd(Tensor3D<double> &tensor, int ranks[3]);    
	
You can call hooi function like:   

        tucker_decomposition<double> A = tucker_hooi(Tensor3D<double> &tensor, int ranks[3], double tol);      

</details>

<details>	
<summary> t-SVD decomposition </summary>

t-SVD algorithm is implemented in t-SVD.cpp.

The decomposition components of t-SVD is defined as:  
>template\<class type\>    
>class tsvd_decomposition{  
>&emsp;&emsp;  Tensor3D\<type\> U, Sigma, V;  
>};  

You can call tsvd function like:   
	
        tsvd_decomposition<double> A = tsvd_decomposition(Tensor3D<double> &tensor);      
</details>

<details>	
<summary> Tensor Train decomposition  </summary>

Tensor Train decomposition via alternating least squares (ALS), which is realized in the Tensor-Train directory.        

You can find TensorTrain class in train.h file in the Tensor-Train directory.    

The decomposition components of tensortrain is defined as:    

You can call cp_als function like:     

       tensortrain_decomposition<double> A = tensortrain_decomposition(Tensor3D<double> &tensor, tol);      

</details>

## API Reference

<details>	
<summary> CANDECOMP/PARAFAC decomposition via alternating least squares (ALS) </summary>

### cp_decomposition\<type\> cp_decomposition(Tensor3D\<type\>& tensor, int rank, int max_iter，type tol);    
#### Source: CP decomposition is realized in cp_als.cpp.    
### Parameters: 
	tensor: the address of tensor; 
	rank: int, number of components;   
	max_iter: int, maximum number of iteration;   
	tol: float, optional  
	(Default: 1e-6) Relative reconstruction error tolerance. The algorithm is considered to have found the global minimum when the reconstruction error is less than tol.  
### Returns:
	cp_decomposition<type>: abstract data type（ADT） for the CP decomposition result.    
	template<class type>  
	class cp_decomposition{  
	    Mat<type> factor[3];  
	};  
	where factor is the matrix list of the corresponding CP decomposition.   

</details>

<details>	
<summary> Tucker decomposition via High Order SVD (HOSVD) and High-Order Orthogonal Iteration (HOOI) </summary>
	
### tucker_decomposition\<type\> tucker_hosvd(Tensor3D\<type\> &tensor, int ranks[3]);      
#### Source: Tucker decomposition is realized in tucker_hosvd.cpp and tucker_hooi.cpp.     

### Parameters:	
	tensor: the address of tensor; 
	ranks: int array; size of the core tensor, (len(ranks) == tensor.ndim);  
	
### tucker_decomposition\<type\> tucker_hooi(Tensor3D\<type\> &tensor, int ranks[3], int max_iter, T tol);  
### Parameters:	
	tensor: the address of tensor; 
	int ranks[3]: size of the core tensor, (len(ranks) == tensor.ndim);  
	init : {‘svd’, ‘random’}, optional;  
	tol : float, optional  
	tolerance: the algorithm stops when the variation in the reconstruction error is less than the tolerance  

### Returns:
	tucker_decomposition<type>: abstract data type（ADT） for the Tucker decomposition result.    
	template<class type>    
	class tucker_decomposition{  
	   Tensor3D<type> core; Mat<type> factor[3];   
	};  
</details>

<details>	
<summary> t-SVD decomposition API </summary>
	
### tsvd_decomposition\<type\> tSVD(Tensor3D\<type\> &tensor);      
#### Source: t-SVD is realized in t-SVD.cpp.     

### Parameters:	
	tensor: the address of tensor; 
	
### Returns:
	tsvd_decomposition<type>: abstract data type（ADT） for the t-SVD decomposition result.    
	class tsvd_decomposition{  
	   Tensor3D<type> U, Sigma, V;  
	};  	

For more details, please refer to the corresponding source files, where all definitations and corresponding illustrations is provied therein.
</details>

<details>	
<summary> Tensor Train decomposition  </summary>
	
### Tensor Train decomposition 
#### Source: Tensor Train decomposition is realized in Tensor-Train/train.h.     
### Parameters:	
	tensor: the address of tensor; 
### Returns:
	tensortrain_decomposition<type>: abstract data type（ADT） for the Tensor Train decomposition result.    
	class tensortrain_decomposition<type> 
	
</details>

## Class List
Here are the classes, structs, unions and interfaces with brief descriptions:

<details>	
<summary>
Tensor3D<type>
</summary>
In TensorLet, all third order tensors are objects of the Tensor3D template class.
	
##### Data Members

int shape[3]; // the dimension of the third order tensor;  
type * p; // a pointer point to tensor.  

##### Public Member Functions
Tensor3D::unfold(tensor, mode)	Returns the mode-mode unfolding of tensor with modes starting at 0.  
Tensor3D::fold(unfolded_tensor, mode, shape)	Refolds the mode-mode unfolding into a tensor of shape shape  
Tensor3D::tens2vec(tensor)	Vectorises a tensor  
Tensor3D::vec2ten(vec, shape)	Folds a vectorised tensor back into a tensor of shape shape  
Tensor3D::frobenius_norm(tensor1, tensor2)	the Frobenius norm of tensors   
Tensor3D::inner(tensor1, tensor2)	Generalised inner products between tensors  
Tensor3D::n_mode_prod(tensor, matrix, mode)	n-mode product of a tensor and a matrix or vector at the specified mode 
Tensor3D::t_prod(tensor1, tensor2)	t-product between tensors

</details>

<details>	
<summary>
cp_decomposition<type>
</summary>
	
##### Public Member Functions  
cp_to_tensor(cp_decomposition &)	Turns the Khatri-product of matrices into a full tensor  
cp_to_unfolded(cp_decomposition &, int mode)	Turns the khatri-product of matrices into an unfolded tensor  
cp_to_vec(cp_decomposition &)	Turns the khatri-product of matrices into a vector  
</details>

<details>	
<summary>
tucker_decomposition<type>
</summary>
	
##### Public Member Functions  
tucker_to_tensor(tucker_decomposition &)	Converts the Tucker tensor into a full tensor  
tucker_to_unfolded(tucker_decomposition &)	Converts the Tucker decomposition into an unfolded tensor (i.e.  
tucker_to_vec(tucker_decomposition &)	Converts a Tucker decomposition into a vectorised tensor  
</details>

<details>	
<summary>
tsvd_decomposition<type>
</summary>
	
##### Public Member Functions   
tsvd_to_tensor(tsvd_decomposition &)	Converts the t-SVD tensor into a full tensor  
tsvd_to_unfolded(tsvd_decomposition &)	Converts the t-SVD decomposition into an unfolded tensor (i.e.  
tsvd_to_vec(tsvd_decomposition &)	Converts a t-SVD decomposition into a vectorised tensor  
</details>

<details>	
<summary>
tensortrain_decomposition<type>
</summary>
	
##### Public Member Functions  
tt_to_tensor(tensortrain_decomposition &)	Converts the TT tensor into a full tensor  
tt_to_unfolded(tensortrain_decomposition &)	Converts the TT decomposition into an unfolded tensor (i.e.  
tt_to_vec(tensortrain_decomposition &)	Converts a TT decomposition into a vectorised tensor  

</details>



## References
<details>	
<summary>
Main references
</summary>
[1] Kolda T G, Bader B W. Tensor decompositions and applications[J]. SIAM review, 2009, 51(3): 455-500.

[2] Kilmer, M. E., Braman, K., Hao, N., & Hoover, R. C. (2013). Third-order tensors as operators on matrices: A theoretical and computational framework with applications in imaging. SIAM Journal on Matrix Analysis and Applications, 34(1), 148-172.

[3] Kjolstad, Fredrik, Shoaib Kamil, Stephen Chou, David Lugato, and Saman Amarasinghe. "The tensor algebra compiler." Proceedings of the ACM on Programming Languages 1, no. OOPSLA (2017): 77.

[4] De Lathauwer L, De Moor B, Vandewalle J. A multilinear singular value decomposition[J]. SIAM journal on Matrix Analysis and Applications, 2000, 21(4): 1253-1278.

[5] Xiao-Yang Liu and Xiaodong Wang. Fourth-order Tensors with Multidimensional Discrete Transforms, 2017. https://arxiv.org/abs/1705.01576

[6] Papalexakis E E, Faloutsos C, Sidiropoulos N D. Tensors for data mining and data fusion: Models, applications, and scalable algorithms[J]. ACM Transactions on Intelligent Systems and Technology (TIST), 2017, 8(2): 16.

[7] Liavas A P, Sidiropoulos N D. Parallel algorithms for constrained tensor factorization via alternating direction method of multipliers[J]. IEEE Transactions on Signal Processing, 2015, 63(20): 5450-5463.

[8] Ravindran N, Sidiropoulos N D, Smith S, et al. Memory-efficient parallel computation of tensor and matrix products for big tensor decomposition[C]//Signals, Systems and Computers, 2014 48th Asilomar Conference on. IEEE, 2014: 581-585.

</details>


