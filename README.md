# Dynamic-Logit-Demand-Estimation
## ロジット型動学需要推定モデル
このモデルは消費者が複数の商品の購入意思決定を複数期間にわたって行うモデルです。通常のロジットモデルは、クロスセクションデータを用いることが多いですが、そのような静学モデルでは消費者の動学的な意思決定を表すことができません。例えば商品が将来的に割引されるなどして商品価格が下がる場合、消費者はそれを予期して買い控えをし、価格が下がってきたタイミングで商品を購入します。動学ロジットモデルでは、消費者の将来的期待を反映した行動もモデルに組み込みます。

## モデル
### ベルマン方程式
消費者 $i$ が $t$ 期に財 $j \in J$ を購入するか購入しないか(outside option)の意思決定をします。財 $j$ を購入することで得られる効用は $\beta_j-\alpha p_{j,t}+\epsilon_{j,t,i}$です。 $\beta,\alpha$ はパラメータ、 $p$ は価格、 $\epsilon$ は攪乱項です。 $\epsilon$　がタイプI極値分布に従うとすると、買い控えする価値はベルマン方程式を用いて以下の様に与えられます。

$$
V_0(p; \theta) = \delta \int_{p} \int_{\epsilon} \\\{ \max \\\{ \underbrace{V_0(p_{j,t+1};\theta)+\epsilon_{0,t,i}}_{\text{購入しない場合の価値}},
\max_j \\\{ \underbrace{\beta_j - \alpha p _{j,t+1} + \epsilon _{j,t,i} } _{\text{jを購入する場合の価値}}     \\\}  \\\}  \\\} d _{p} F(p)
$$

$$
=\delta \int_{p} log\left(exp(V_0(p_{j,t+1}; \theta))+
{\large\Sigma_{j}} exp(\beta_{j}-\alpha p_{j,t+1}\right)d_{p}F(p)
$$

また価格は以下の様なAR(1)過程で遷移するとします。

$$
p_{j,t+1} =(\rho_{j,0}\space\rho_{j,1}) 
\begin{pmatrix}
1 \\
p_{j,t}
\end{pmatrix}
+\psi_{i,t}=p _{t}^{'} \rho _{j} + \psi _{j,t}\hspace{5pt}\text{for}\hspace{5pt}j = 1,2,...J
$$

すると $V_0$ は以下の様になります。

$$
V_0(p; \theta)=\delta \int_{\psi} 
log\left(exp(V_0( p_{t}^{'} \rho_{j}+\psi_{j,t}; \theta))+
{\large\Sigma_{j}} exp(\beta_{j}
-\alpha ( p_{t}^{'} \rho_{j}+\psi_{j,t})
\right)d_{\psi}F(\psi)
$$

$V_0$ は $T(V_0) - V_0 = 0$ を解くことによって得られます。 $T$ はベルマンオペレータです。

### 推定
$j$ を選択する確率は $P_j(p;\theta)f_{p}(p) = P_{j}(p;\theta)f_{\psi}(\psi)|J|$ より以下の様になります。

$$
P_i(\text{decision} = j|\psi_t; \theta) = \frac{\exp(\beta_{j} - \alpha p_{j,t})}
{\exp(V_0(p_{t}; \theta)) + \Sigma_j\exp(\beta_j - \alpha p_{j,t})}
f(\psi_{j=1,t},\psi_{j=2,t},...,\psi_{j=J,t})|J_{p \rightarrow \psi}| 
$$

$$
P_i(\text{decision} = 0|\psi_{t}; \theta) = \frac{\exp(V_0(p_{t};\theta))}
{\exp(V_0(p_{t};\theta)) + \Sigma_j\exp(\beta_{j}-\alpha p_{j,t})}
f(\psi_{j=1,t},\psi_{j=2,t},...,\psi_{j=J,t})|J_{p \rightarrow \psi}| 
$$

対数尤度関数は以下の様になります。

$$
L(\theta) = {\large\Sigma_{i=1}}{\large\Sigma_{t=2}}\log(P_i(decision|\psi_{t};\theta))
$$

ヤコビ行列

$$
\begin{equation}
\psi_{jt} = p_{jt} - \rho_{j,0} - \rho_{j,1} p_{j,t-1} \overset{\text{def}}{=} G
\end{equation}
$$

$$
\\| \mathbf{J} _{ {\psi} \rightarrow p}  \\| = 
\begin{vmatrix}
\frac{\partial G}{ \partial p _{j,t} }
\end{vmatrix} = 
\begin{vmatrix}
1 \\
\end{vmatrix}
$$

 
 ここではRust(1987)のような[NFXP](https://www.jstor.org/stable/1911259)アルゴリズムを採用しますが、 $T(V_0) - V_0 = 0$ を制約条件として[MPEC](https://onlinelibrary.wiley.com/doi/abs/10.3982/ECTA7925)アルゴリズムで推定することも可能です。

