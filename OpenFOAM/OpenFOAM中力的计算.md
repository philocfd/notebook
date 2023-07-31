# 升阻力系数计算
## 力的计算
![image.png](https://cdn.nlark.com/yuque/0/2022/png/12388090/1656923980845-d0da7961-57e4-4162-96d2-361a998de606.png#clientId=u11dfb93d-9e9c-4&from=paste&height=272&id=u4c092f55&originHeight=559&originWidth=554&originalType=binary&ratio=1&rotation=0&showTitle=false&size=43344&status=done&style=none&taskId=u33df8240-6b48-46ab-a222-03216a38494&title=&width=270)
算例设置
合力的计算按照下式进行：
$\textbf{F} = \textbf{F}_p+\textbf{F}_v = \sum_i\rho_i\textbf{s}_{f,i}(p_i-p_{ref}) + \sum_{i}\textbf{s}_{f,i}\cdot(\mu \textbf{R}_{dev})$
粘性力的计算如下：
```cpp
-rho()*nu*dev(twoSymm(fvc::grad(U)))
```
```cpp
  for (const label patchi : patchSet_)
        {
            const vectorField& d = mesh_.C().boundaryField()[patchi];
 
            const vectorField Md(d - origin);
           //计算压力
            const vectorField fP
            (
                rhoRef*Sfb[patchi]*(p.boundaryField()[patchi] - pRef)
            );
           //计算粘性力
            const vectorField fV(Sfb[patchi] & devRhoReffb[patchi]);
 
            addToPatchFields(patchi, Md, fP, fV);
        }
```
其中，$\rho$是密度，$\textbf{s}_{f,i}$是面的面积向量，$p$是压力，$\mu$是运动学粘性系数， $\textbf{R}_{dev}$是偏应力张量。
```cpp
forceCoeffs1
        {
            type            forceCoeffs;
        libs ( "libforces.so" );
        writeControl   timeStep;
        writeInterval  1;
        patches         (airfoil);
        rho             rhoInf;
        log             true;
        rhoInf          1;
        liftDir         ( 0  1  0);
        dragDir         (1  0  0);
        CofR            (0.25 0 0);
        pitchAxis       (0 0 1);
        magUInf         1;
        lRef            1;
        Aref            1;
        }
```

升力系数，阻力系数，扭矩系数
$P_{dyn} = 0.5\rho_{ref}*U_{inf}^2$
![image.png](https://cdn.nlark.com/yuque/0/2022/png/12388090/1656924289595-3677610f-2bec-44c0-896d-7719612df1e1.png#clientId=u11dfb93d-9e9c-4&from=paste&height=420&id=uab92efde&originHeight=420&originWidth=390&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23244&status=done&style=stroke&taskId=ub27755fb-7b52-4411-b5b6-bd678710085&title=&width=390)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/12388090/1656924321897-43a9b013-3d1f-4ebf-90c5-d66465831ba4.png#clientId=u11dfb93d-9e9c-4&from=paste&height=355&id=u8ef3f108&originHeight=355&originWidth=404&originalType=binary&ratio=1&rotation=0&showTitle=false&size=17485&status=done&style=stroke&taskId=u9afc8913-fd4a-4396-bd36-48152682579&title=&width=404)
```cpp
        scalar pDyn = 0.5*rhoRef_*magUInf_*magUInf_;

        Field<vector> totForce(force_[0] + force_[1] + force_[2]);
        Field<vector> totMoment(moment_[0] + moment_[1] + moment_[2]);

        List<Field<scalar> > coeffs(3);
        coeffs[0].setSize(nBin_);
        coeffs[1].setSize(nBin_);
        coeffs[2].setSize(nBin_);

        // lift, drag and moment
        coeffs[0] = (totForce & liftDir_)/(Aref_*pDyn);
        coeffs[1] = (totForce & dragDir_)/(Aref_*pDyn);
        coeffs[2] = (totMoment & pitchAxis_)/(Aref_*lRef_*pDyn);

        scalar Cl = sum(coeffs[0]);
        scalar Cd = sum(coeffs[1]);
        scalar Cm = sum(coeffs[2]);

        scalar Clf = Cl/2.0 + Cm;
        scalar Clr = Cl/2.0 - Cm;
```
