[![](/Content/images/codebus-white.svg)](/)

# [Margoo](/margoo/)

...?

  * [管理](/console/posts)



##  基于 EasyX 的 BVH 优化 、PDF 非均匀采样降噪、多线程渲染的光线追踪系统+基础原理讲解 [![金牌收录](/content/images/gold.png)](?medal=3 "金牌收录")

__2023-1-19 ~ 2025-11-22 __[Margoo](/margoo/) [ __(8)](https://codebus.cn/margoo/ray-tracing-plus#comment)

# 代码运行环境

  * Visual Studio 2022，EasyX_20220901
  * Windows 10 及以上版本操作系



# BVH 优化介绍

BVH 即为层次包围盒（Bounding Volume Hierarchies），是利用体型略大但是几何特征简单的包围盒来近似描述复杂的几何对象，在光线追踪的时候，可以先判断包围盒是否和光线相交，再进行更细致的判断，在复杂场景下有利于提高程序性能。

# PDF 非均匀采样介绍

PDF 非均匀采样优化的原理：对于普通的 Monte Carlo（国内一般译作蒙特卡洛或蒙特卡罗） 进行计算的话，则有如下的表达式:

普通未加权重的蒙特卡洛积分表达式

其中，N 即为采样率，根据大数定理，当 N 趋向于无穷的时候，结果也会趋向于期望值，即为真实值（该结论的证明在此处暂不提供），而光追本质上也是一个运用 Monte Carlo 进行光子模拟，因此，如果直接使用上述直接进行光子模拟，只有通过提高 N 来逼近期望图像（也就是真实世界的光线追踪图像），然而，只通过提高 N 会导致一个问题：有的部分区域并不需要进行过多的采样，提高 N 会导致相对多余的计算，所以引入了 PDF 非均匀采样，PDF 非均匀采样中，是按照概率密度进行采样，出现概率更大的点，计算时的权重就更低，因此，原来的等式可修改为：

其实最开始的式子，就是默认带入得到的。

那么在实际的代码中，可以通过计算折射光线可能击中的点的概率密度来进行采样，这样可以达到降噪效果

# 多线程渲染原理

多线程渲染的原理很简单，就是把画布分割为很多小块，不同小块由不同的线程负责渲染。

# 一些可能问题的 FAQ

Q：我打开了程序，但是为什么程序一直是黑的？

A：可能是因为你的电脑性能过差且没有开启多线程渲染模式，实际上电脑一直在渲染，只不过过于缓慢还在渲染黑色天空的部分。

Q：我也用相同的测试配置跑了程序，为什么程序却没有测试中的那么快？

A：可能是因为你使用了 debug 模式编译出来的代码，实际测试 debug 模式和 release 模式下渲染的速度相差很大。

Q：我打开了程序，为什么鼠标无法移动/鼠标移动缓慢？

A：多线程渲染的时候可能会造成电脑卡顿，如果你在进行多线程渲染的情况下电脑鼠标暂时移动缓慢或者无法移动属于正常现象，该现象会持续到图像渲染完毕为止。

# 渲染结果预览

![](/f/a/0/0/615/Capture%201.png)

![](/f/a/0/0/615/Capture%204.png)

![](/f/a/0/0/615/Capture%202.png)

![](/f/a/0/0/615/Capture%203.png)

# 完整代码
    
    
    /*
     *	程序名称 ：光线追踪：PDF、BVH 优化
     *	作　　者 ：Margoo
     *	邮　　箱 ：1683691371@qq.com
     */
    #include <algorithm>
    #include <conio.h>
    #include <functional>
    #include <graphics.h>
    #include <intrin.h>
    #include <iomanip>
    #include <memory>
    #include <sstream>
    #include <string>
    #include <thread>
    #include <vector>
    #include <xmmintrin.h>
    
     /*
      * 常用常量定义
      */
    const float Inf = std::numeric_limits<float>::infinity();
    const float PI = 3.1415926535897932385f;
    
    /*
     * 角度转为弧度
     */
    float DegreesToRadians(float Degrees)
    {
    	return Degrees * PI / 180.f;
    }
    
    float Random()
    {
    	return rand() / (RAND_MAX + 1.f);
    }
    float Random(float Max, float Min)
    {
    	return Min + (Max - Min) * float(Random());
    }
    float Range(float X, float Min, float Max)
    {
    	if (X < Min)
    	{
    		return Min;
    	}
    	if (X > Max)
    	{
    		return Max;
    	}
    
    	return X;
    }
    
    /*
     * 三维向量类 Vector3（同时该类也充当了三维点和颜色）
     */
    typedef class Vector3
    {
    public:
    	Vector3() : Element{ 0.f, 0.f, 0.f }
    	{
    	}
    	Vector3(float X, float Y, float Z) : Element{ X, Y, Z }
    	{
    	}
    
    	float GetX() const
    	{
    		return Element[0];
    	}
    	float GetY() const
    	{
    		return Element[1];
    	}
    	float GetZ() const
    	{
    		return Element[2];
    	}
    
    	Vector3 operator-() const
    	{
    		return Vector3(-Element[0], -Element[1], -Element[2]);
    	}
    	float operator[](const unsigned int& Position) const
    	{
    		if (Position >= 3)
    		{
    			abort();
    
    			return Element[0];
    		}
    		else
    		{
    			return Element[Position];
    		}
    	}
    	float& operator[](const unsigned int& Position)
    	{
    		if (Position >= 3)
    		{
    			abort();
    
    			return Element[0];
    		}
    		else
    		{
    			return Element[Position];
    		}
    	}
    	Vector3& operator+=(const Vector3& Value)
    	{
    		Element[0] += Value.Element[0];
    		Element[1] += Value.Element[1];
    		Element[2] += Value.Element[2];
    
    		return *this;
    	}
    	Vector3& operator*=(const float& Value)
    	{
    		Element[0] *= Value;
    		Element[1] *= Value;
    		Element[2] *= Value;
    
    		return *this;
    	}
    	static Vector3 RandomToSphere(float Radius, float DistanceSquared)
    	{
    		auto R1 = ::Random();
    		auto R2 = ::Random();
    		auto Z = 1.f + R2 * (sqrt(1.f - Radius * Radius / DistanceSquared) - 1.f);
    
    		auto Phi = 2.f * PI * R1;
    		auto X = cos(Phi) * sqrt(1.f - Z * Z);
    		auto Y = sin(Phi) * sqrt(1.f - Z * Z);
    
    		return Vector3(X, Y, Z);
    	}
    	static Vector3 Random()
    	{
    		return Vector3(::Random(), ::Random(), ::Random());
    	}
    	static Vector3 Random(float Min, float Max)
    	{
    		return Vector3(::Random(Min, Max), ::Random(Min, Max), ::Random(Min, Max));
    	}
    	Vector3& operator/=(const float& Value)
    	{
    		return *this *= 1.f / Value;
    	}
    
    	float LengthSquared() const
    	{
    		return Element[0] * Element[0] + Element[1] * Element[1] + Element[2] * Element[2];
    	}
    	float Length() const
    	{
    		return sqrt(LengthSquared());
    	}
    
    	inline friend Vector3 operator+(const Vector3& Left, const Vector3& Right)
    	{
    		return Vector3(Left.GetX() + Right.GetX(), Left.GetY() + Right.GetY(), Left.GetZ() + Right.GetZ());
    	}
    	inline friend Vector3 operator-(const Vector3& Left, const Vector3& Right)
    	{
    		return Vector3(Left.GetX() - Right.GetX(), Left.GetY() - Right.GetY(), Left.GetZ() - Right.GetZ());
    	}
    	inline friend Vector3 operator*(const Vector3& Left, const Vector3& Right)
    	{
    		return Vector3(Left.GetX() * Right.GetX(), Left.GetY() * Right.GetY(), Left.GetZ() * Right.GetZ());
    	}
    	inline friend Vector3 operator*(const Vector3& Left, const float& Right)
    	{
    		return Vector3(Left.GetX() * Right, Left.GetY() * Right, Left.GetZ() * Right);
    	}
    	inline friend Vector3 operator*(const float& Right, const Vector3& Left)
    	{
    		return Vector3(Left.GetX() * Right, Left.GetY() * Right, Left.GetZ() * Right);
    	}
    	inline friend Vector3 operator/(const Vector3& Left, const float& Right)
    	{
    		return Vector3(Left.GetX() / Right, Left.GetY() / Right, Left.GetZ() / Right);
    	}
    
    	static Vector3 UnitVector(Vector3 Vector)
    	{
    		return Vector / Vector.Length();
    	}
    
    	static Vector3 RandomUnitVector()
    	{
    		return UnitVector(RandomInUnitSphere());
    	}
    
    	static Vector3 RandomInUnitSphere()
    	{
    		auto A = ::Random(0.f, 2.f * PI);
    		auto Z = ::Random(-1.f, 1.f);
    		auto R = sqrt(1.f - Z * Z);
    
    		return Vector3(R * cos(A), R * sin(A), Z);
    	}
    
    private:
    	float Element[3];
    } Point3, Color;
    
    /*
     * 向量运算函数
     */
    inline float Vector3Dot(const Vector3& Left, const Vector3& Right)
    {
    	return Left[0] * Right[0] + Left[1] * Right[1] + Left[2] * Right[2];
    }
    inline Vector3 Vector3Cross(const Vector3& Left, const Vector3& Right)
    {
    	return Vector3(Left[1] * Right[2] - Left[2] * Right[1], Left[2] * Right[0] - Left[0] * Right[2],
    		Left[0] * Right[1] - Left[1] * Right[0]);
    }
    inline Vector3 UnitVector(Vector3 Vector)
    {
    	return Vector3::UnitVector(Vector);
    }
    
    /*
     * 判断法向量是否在同一个半球。
     */
    Vector3 RandomInHemisphere(const Vector3& NormalSurface)
    {
    	Vector3 InUnitSphere = Vector3::RandomInUnitSphere();
    	if (Vector3Dot(InUnitSphere, NormalSurface) > 0.f)
    	{
    		return InUnitSphere;
    	}
    	else
    	{
    		return -InUnitSphere;
    	}
    }
    
    /*
     * 光线类
     */
    class Ray
    {
    public:
    	Ray()
    	{
    	}
    	Ray(const Point3& OriginPoint, const Vector3& LightDirection) : LightOrigin(OriginPoint), Direction(LightDirection)
    	{
    	}
    
    	Point3 OriginPoint() const
    	{
    		return LightOrigin;
    	}
    	Vector3 LightDirection() const
    	{
    		return Direction;
    	}
    
    	Point3 LightAt(const float& T) const
    	{
    		return LightOrigin + T * Direction;
    	}
    
    	static Vector3 RandomRay()
    	{
    		return Vector3(Random(), Random(), Random());
    	}
    	static Vector3 RandomRay(float Min, float Max)
    	{
    		return Vector3(Random(Min, Max), Random(Min, Max), Random(Min, Max));
    	}
    
    public:
    	// 光线出发点
    	Point3	LightOrigin;
    	// 光线方向
    	Vector3 Direction;
    };
    
    /*
     * BVH 优化
     */
    class BVHBox
    {
    public:
    	BVHBox()
    	{
    	}
    	BVHBox(const Vector3& Min, const Vector3& Max)
    	{
    		BoxMin = Min;
    		BoxMax = Max;
    	}
    
    	bool Hit(const Ray& RayInstance, float TMin, float TMax) const
    	{
    		for (auto Count = 0; Count < 3; ++Count)
    		{
    			auto InvD = 1.f / RayInstance.Direction[Count];
    			auto T0 = (BoxMin[Count] - RayInstance.LightOrigin[Count]) * InvD;
    			auto T1 = (BoxMax[Count] - RayInstance.LightOrigin[Count]) * InvD;
    
    			if (InvD < 0.f)
    			{
    				auto Temp = T0;
    
    				T0 = T1;
    				T1 = Temp;
    			}
    
    			TMin = T0 > TMin ? T0 : TMin;
    			TMax = T1 < TMax ? T1 : TMax;
    
    			if (TMax <= TMin)
    			{
    				return false;
    			}
    		}
    
    		return true;
    	}
    
    public:
    	Vector3 BoxMin;
    	Vector3 BoxMax;
    };
    
    /*
     * 标准正交基类
     */
    class ONB
    {
    public:
    	ONB()
    	{
    	}
    
    	Vector3 operator[](const int& Position) const
    	{
    		return Axis[Position];
    	}
    
    	Vector3 GetU() const
    	{
    		return Axis[0];
    	}
    	Vector3 GetV() const
    	{
    		return Axis[1];
    	}
    	Vector3 GetW() const
    	{
    		return Axis[2];
    	}
    
    	Vector3 Local(float A, float B, float C) const
    	{
    		return A * GetU() + B * GetV() + C * GetW();
    	}
    	Vector3 Local(const Vector3& A) const
    	{
    		return A.GetX() * GetU() + A.GetY() * GetV() + A.GetZ() * GetW();
    	}
    
    	void BuildFromW(const Vector3& N)
    	{
    		Axis[2] = UnitVector(N);
    
    		Vector3 A = (fabs(GetW().GetX()) > 0.9) ? Vector3(0.f, 1.f, 0.f) : Vector3(1.f, 0.f, 0.f);
    
    		Axis[1] = UnitVector(Vector3Cross(GetW(), A));
    		Axis[0] = Vector3Cross(GetW(), GetV());
    	}
    
    public:
    	Vector3 Axis[3];
    };
    
    /*
     * 一个三维对象的材质基类
     */
    class ObjectMaterial;
    
    /*
     * 定义了光线求交所需数据
     */
    struct HitData
    {
    	Vector3			NormalSurface;
    	Vector3			RayData;
    	float			T;
    	float			U;
    	float			V;
    	bool			FrontFace;
    	ObjectMaterial* Material;
    
    	void			SetSurfaceNormal(const Ray& RayInstance, const Vector3& OutsideNormal)
    	{
    		FrontFace = Vector3Dot(RayInstance.Direction, OutsideNormal) < 0;
    		NormalSurface = FrontFace ? OutsideNormal : -OutsideNormal;
    	}
    };
    
    class PDF;
    
    Vector3 RandomCosineDirection()
    {
    	auto R1 = float(Random());
    	auto R2 = float(Random());
    	auto Z = sqrt(1.f - R2);
    
    	auto PHI = 2.f * PI * R1;
    	auto X = cos(PHI) * sqrt(R2);
    	auto Y = sin(PHI) * sqrt(R2);
    
    	return Vector3(X, Y, Z);
    }
    
    struct ScatterData
    {
    	Ray		SpecularRay;
    	bool	IsSpecular;
    	Vector3 Attenuation;
    	PDF* PDF;
    };
    class PDF
    {
    public:
    	virtual ~PDF()
    	{
    	}
    
    	virtual float	GetValue(const Vector3& Direction) const = 0;
    	virtual Vector3 Generate() const = 0;
    };
    
    class CosinePDF : public PDF
    {
    public:
    	CosinePDF(const Vector3& W)
    	{
    		UVW.BuildFromW(W);
    	}
    
    	float GetValue(const Vector3& Direction) const override
    	{
    		auto Cosine = Vector3Dot(UnitVector(Direction), UVW.GetW());
    
    		return (Cosine <= 0.f) ? 0.f : Cosine / PI;
    	}
    	Vector3 Generate() const override
    	{
    		return UVW.Local(RandomCosineDirection());
    	}
    
    public:
    	ONB UVW;
    };
    
    /*
     * 材质基类
     */
    class ObjectMaterial
    {
    public:
    	/*
    	 * 用于实现光源的 Emitted，如果该物质本身不发光，返回纯黑
    	 */
    	virtual Vector3 Emitted(float U, float V, const HitData& Data, const Vector3& RayData)
    	{
    		return Vector3(0.f, 0.f, 0.f);
    	}
    
    	virtual bool Scatter(const Ray& RayIn, const HitData& HitData, ScatterData& ScatterData) const
    	{
    		return false;
    	}
    
    	virtual double ScatteringPDF(const Ray& RayIn, const HitData& HitData, const Ray& ScatteredRay) const
    	{
    		return 0;
    	}
    
    public:
    	/*
    	 * 光反射计算
    	 */
    	Vector3 Reflect(const Vector3& RayIn, const Vector3& NormalRay) const
    	{
    		return RayIn - 2.f * Vector3Dot(RayIn, NormalRay) * NormalRay;
    	}
    };
    
    /*
     * 材质的基类
     */
    class Texture
    {
    public:
    	virtual Vector3 GetValue(float U, float V, const Vector3& Point) const = 0;
    };
    
    /*
     * 固定颜色的材质
     */
    class ConstantTexture : public Texture
    {
    public:
    	ConstantTexture()
    	{
    	}
    	ConstantTexture(Vector3 TextureColor) : Color(TextureColor)
    	{
    	}
    	Vector3 GetValue(float U, float V, const Vector3& Point) const override
    	{
    		return Color;
    	}
    
    public:
    	Vector3 Color;
    };
    /*
     * 棋盘材质
     */
    class CheckerTexture : public Texture
    {
    public:
    	CheckerTexture()
    	{
    	}
    	CheckerTexture(Texture* Even, Texture* Odd) : EvenTexture(Even), OddTexture(Odd)
    	{
    	}
    	Vector3 GetValue(float U, float V, const Vector3& Point) const override
    	{
    		auto Sines =
    			sin(10.f * Point.GetX()) * sin(10.f * Point.GetY()) * sin(10.f * Point.GetZ());
    		if (Sines < 0.f)
    		{
    			return OddTexture->GetValue(U, V, Point);
    		}
    		else
    		{
    			return EvenTexture->GetValue(U, V, Point);
    		}
    	}
    
    public:
    	Texture* EvenTexture;
    	Texture* OddTexture;
    };
    
    /*
     * 漫反射材质（又名朗伯材质）
     */
    class LambertianMaterial : public ObjectMaterial
    {
    public:
    	LambertianMaterial(Texture* MaterialTexture) : Texture(MaterialTexture)
    	{
    	}
    	bool Scatter(const Ray& RayIn, const HitData& HitData, ScatterData& ScatterData) const override
    	{
    		ScatterData.IsSpecular = false;
    		ScatterData.Attenuation = Texture->GetValue(HitData.U, HitData.V, HitData.RayData);
    		ScatterData.PDF = new CosinePDF(HitData.NormalSurface);
    
    		return true;
    	}
    	double ScatteringPDF(const Ray& RayIn, const HitData& HitData, const Ray& ScatteredRay) const override
    	{
    		auto Cosine = Vector3Dot(HitData.NormalSurface, UnitVector(ScatteredRay.Direction));
    		return Cosine < 0.f ? 0.f : Cosine / PI;
    	}
    
    public:
    	Texture* Texture;
    };
    
    class MetalMaterial : public ObjectMaterial
    {
    public:
    	MetalMaterial(const Vector3& Albedo, float Fuzz) : MaterialAlbedo(Albedo), MetalFuzz(Fuzz < 1.f ? Fuzz : 1.f)
    	{
    	}
    	bool Scatter(const Ray& RayIn, const HitData& HitData, ScatterData& ScatterData) const override
    	{
    		Vector3 Reflected = Reflect(UnitVector(RayIn.Direction), HitData.NormalSurface);
    
    		ScatterData.SpecularRay = Ray(HitData.RayData, Reflected + MetalFuzz * Vector3::RandomInUnitSphere());
    		ScatterData.Attenuation = MaterialAlbedo;
    		ScatterData.IsSpecular = true;
    		ScatterData.PDF = 0;
    
    		return true;
    	}
    
    public:
    	float	MetalFuzz;
    	Vector3 MaterialAlbedo;
    };
    
    class DielectricMaterial : public ObjectMaterial
    {
    public:
    	DielectricMaterial(float RefractiveIndex) : MaterialRefractiveIndex(RefractiveIndex)
    	{
    	}
    	bool Scatter(const Ray& RayIn, const HitData& HitData, ScatterData& ScatterData) const override
    	{
    		ScatterData.IsSpecular = true;
    		ScatterData.PDF = nullptr;
    		ScatterData.Attenuation = Vector3(1.0f, 1.0f, 1.0f);
    		float	RefractionRatio = HitData.FrontFace ? (1.0f / MaterialRefractiveIndex) : MaterialRefractiveIndex;
    
    		Vector3 UnitDirection = UnitVector(RayIn.Direction);
    		float	CosTheta = min(Vector3Dot(-UnitDirection, HitData.NormalSurface), 1.0f);
    		float	SinTheta = sqrt(1.0f - CosTheta * CosTheta);
    
    		bool	AbleToRefract = RefractionRatio * SinTheta > 1.0;
    		Vector3 Direction;
    
    		if (AbleToRefract || Schlick(CosTheta, RefractionRatio) > Random())
    		{
    			Direction = Reflect(UnitDirection, HitData.NormalSurface);
    		}
    		else
    		{
    			Direction = Refract(UnitDirection, HitData.NormalSurface, RefractionRatio);
    		}
    
    		ScatterData.SpecularRay = Ray(HitData.RayData, Direction);
    
    		return true;
    	}
    
    public:
    	Vector3 Refract(const Vector3& UV, const Vector3& RayIn, float EtaiOverEtat) const
    	{
    		auto	CosTheta = Vector3Dot(-UV, RayIn);
    
    		Vector3 OutParallel = EtaiOverEtat * (UV + CosTheta * RayIn);
    		Vector3 OutPerp = -sqrt(1.f - OutParallel.LengthSquared()) * RayIn;
    
    		return OutParallel + OutPerp;
    	}
    	float Schlick(float Cos, float RefractiveIndex) const
    	{
    		auto R0 = (1.f - RefractiveIndex) / (1.f + RefractiveIndex);
    		R0 *= R0;
    
    		return R0 + (1.f - R0) * pow((1.f - Cos), 5);
    	}
    
    private:
    	float MaterialRefractiveIndex;
    };
    
    /*
     * 光源材质
     */
    class DiffuseLight : public ObjectMaterial
    {
    public:
    	DiffuseLight(Vector3 Color) : LightColor(Color)
    	{
    	}
    
    	/*
    	 * 光源则返回光源的颜色
    	 */
    	Vector3 Emitted(float U, float V, const HitData& Data, const Vector3& RayData) override
    	{
    		if (Data.FrontFace)
    		{
    			return LightColor;
    		}
    		else
    		{
    			return Vector3(0.f, 0.f, 0.f);
    		}
    	}
    
    public:
    	Vector3 LightColor;
    };
    
    class HitTestBase
    {
    public:
    	virtual bool   HitTest(const Ray& RayInstance, float MinT, float MaxT, HitData& Data) const = 0;
    	// 用于 BVH 优化的盒型模型
    	virtual bool   HittingBox(float T0, float T1, BVHBox& Box) const = 0;
    
    	virtual float PDFValue(const Vector3& OriginPoint, const Vector3& Vector) const
    	{
    		return 0.f;
    	}
    	virtual Vector3 Random(const Vector3& Origin) const
    	{
    		return Vector3(1.f, 0.f, 0.f);
    	}
    
    	BVHBox GetBox(const BVHBox& FirstBox, const BVHBox& SecondBox) const
    	{
    		Vector3 Min(min(FirstBox.BoxMin.GetX(), SecondBox.BoxMin.GetX()),
    			min(FirstBox.BoxMin.GetY(), SecondBox.BoxMin.GetY()),
    			min(FirstBox.BoxMin.GetZ(), SecondBox.BoxMin.GetZ()));
    		Vector3 Max(max(FirstBox.BoxMax.GetX(), SecondBox.BoxMax.GetX()),
    			max(FirstBox.BoxMax.GetY(), SecondBox.BoxMax.GetY()),
    			max(FirstBox.BoxMax.GetZ(), SecondBox.BoxMax.GetZ()));
    
    		return BVHBox(Min, Max);
    	}
    };
    class ObjectHitPDF : public PDF
    {
    public:
    	ObjectHitPDF(HitTestBase* Object, const Vector3& Origin) : ObjectRef(Object), OriginPoint(Origin)
    	{
    	}
    
    	float GetValue(const Vector3& Direction) const override
    	{
    		return ObjectRef->PDFValue(OriginPoint, Direction);
    	}
    	Vector3 Generate() const override
    	{
    		return ObjectRef->Random(OriginPoint);
    	}
    
    public:
    	Vector3		 OriginPoint;
    	HitTestBase* ObjectRef;
    };
    
    class XYRect : public HitTestBase
    {
    public:
    	XYRect() : Material(nullptr)
    	{
    	}
    
    	XYRect(float IX0, float IX1, float IY0, float IY1, float IK, ObjectMaterial* IMaterial)
    		: X0(IX0), X1(IX1), Y0(IY0), Y1(IY1), K(IK), Material(IMaterial)
    	{
    	}
    
    	float PDFValue(const Vector3& Origin, const Vector3& Vector) const override
    	{
    		HitData Data;
    		if (!HitTest(Ray(Origin, Vector), 0.001f, Inf, Data))
    		{
    			return 0.f;
    		}
    
    		auto Area = (X1 - X0) * (Y1 - Y0);
    		auto DistanceSuqared = Data.T * Data.T * Vector.LengthSquared();
    		auto Cosine = float(fabs(Vector3Dot(Vector, Data.NormalSurface) / Vector.Length()));
    
    		return DistanceSuqared / (Cosine * Area);
    	}
    	Vector3 Random(const Vector3& Origin) const override
    	{
    		auto RandomPoint = Vector3(::Random(X0, X1), ::Random(Y0, Y1), K);
    		return RandomPoint - Origin;
    	}
    
    	bool HitTest(const Ray& RayInstance, float T0, float T1, HitData& Data) const override
    	{
    		auto T = (K - RayInstance.LightOrigin.GetZ()) / RayInstance.Direction.GetZ();
    		if (T < T0 || T > T1)
    		{
    			return false;
    		}
    
    		auto  X = RayInstance.LightOrigin.GetX() + T * RayInstance.Direction.GetX();
    		auto  Y = RayInstance.LightOrigin.GetY() + T * RayInstance.Direction.GetY();
    
    		float OX0;
    		float OX1;
    		float OY0;
    		float OY1;
    
    		OX0 = (const float)X0;
    		OX1 = (const float)X1;
    		OY0 = (const float)Y0;
    		OY1 = (const float)Y1;
    
    		if (X < OX0 || X > OX1 || Y < OY0 || Y > OY1)
    		{
    			return false;
    		}
    
    		Data.U = (X - OX0) / (OX1 - OX0);
    		Data.V = (Y - OY0) / (OY1 - OY0);
    		Data.T = T;
    
    		Vector3 OutwardNormal = Vector3(0.f, 0.f, 1.f);
    
    		Data.SetSurfaceNormal(RayInstance, OutwardNormal);
    		Data.Material = Material;
    		Data.RayData = RayInstance.LightAt(T);
    
    		return true;
    	}
    	bool HittingBox(float T0, float T1, BVHBox& Box) const override
    	{
    		Box = BVHBox(Vector3(X0, Y0, K - 0.0001f), Vector3(X1, Y1, K + 0.001f));
    
    		return true;
    	}
    
    public:
    	float			X0;
    	float			X1;
    	float			Y0;
    	float			Y1;
    	float			K;
    
    	ObjectMaterial* Material;
    };
    class XZRect : public HitTestBase
    {
    public:
    	XZRect() : Material(nullptr)
    	{
    	}
    
    	XZRect(float IX0, float IX1, float IZ0, float IZ1, float IK, ObjectMaterial* IMaterial)
    		: X0(IX0), X1(IX1), Z0(IZ0), Z1(IZ1), K(IK), Material(IMaterial)
    	{
    	}
    
    	bool HitTest(const Ray& RayInstance, float T0, float T1, HitData& Data) const override
    	{
    		auto T = (K - RayInstance.LightOrigin.GetY()) / RayInstance.Direction.GetY();
    		if (T < T0 || T > T1)
    		{
    			return false;
    		}
    
    		auto  X = RayInstance.LightOrigin.GetX() + T * RayInstance.Direction.GetX();
    		auto  Z = RayInstance.LightOrigin.GetZ() + T * RayInstance.Direction.GetZ();
    
    		float OX0;
    		float OX1;
    		float OZ0;
    		float OZ1;
    
    		OX0 = (const float)X0;
    		OX1 = (const float)X1;
    		OZ0 = (const float)Z0;
    		OZ1 = (const float)Z1;
    
    		if (X < OX0 || X > OX1 || Z < OZ0 || Z > OZ1)
    		{
    			return false;
    		}
    
    		Data.U = (X - OX0) / (OX1 - OX0);
    		Data.V = (Z - OZ0) / (OZ1 - OZ0);
    		Data.T = T;
    
    		Vector3 OutwardNormal = Vector3(0.f, 1.f, 0.f);
    
    		Data.SetSurfaceNormal(RayInstance, OutwardNormal);
    		Data.Material = Material;
    		Data.RayData = RayInstance.LightAt(T);
    
    		return true;
    	}
    	bool HittingBox(float T0, float T1, BVHBox& Box) const override
    	{
    		Box = BVHBox(Vector3(X0, K - 0.0001f, Z0), Vector3(X1, K + 0.001f, Z1));
    
    		return true;
    	}
    
    	float PDFValue(const Vector3& Origin, const Vector3& Vector) const override
    	{
    		HitData Data;
    		if (!HitTest(Ray(Origin, Vector), 0.001f, Inf, Data))
    		{
    			return 0.f;
    		}
    
    		auto Area = (X1 - X0) * (Z1 - Z0);
    		auto DistanceSuqared = Data.T * Data.T * Vector.LengthSquared();
    		auto Cosine = float(fabs(Vector3Dot(Vector, Data.NormalSurface) / Vector.Length()));
    
    		return DistanceSuqared / (Cosine * Area);
    	}
    	Vector3 Random(const Vector3& Origin) const override
    	{
    		auto RandomPoint = Vector3(::Random(X0, X1), K, ::Random(Z0, Z1));
    		return RandomPoint - Origin;
    	}
    
    public:
    	float			X0;
    	float			X1;
    	float			Z0;
    	float			Z1;
    	float			K;
    
    	ObjectMaterial* Material;
    };
    class YZRect : public HitTestBase
    {
    public:
    	YZRect() : Material(nullptr)
    	{
    	}
    
    	YZRect(float IY0, float IY1, float IZ0, float IZ1, float IK, ObjectMaterial* IMaterial)
    		: Y0(IY0), Y1(IY1), Z0(IZ0), Z1(IZ1), K(IK), Material(IMaterial)
    	{
    	}
    
    	float PDFValue(const Vector3& Origin, const Vector3& Vector) const override
    	{
    		HitData Data;
    		if (!HitTest(Ray(Origin, Vector), 0.001f, Inf, Data))
    		{
    			return 0.f;
    		}
    
    		auto Area = (Y1 - Y0) * (Y1 - Y0);
    		auto DistanceSuqared = Data.T * Data.T * Vector.LengthSquared();
    		auto Cosine = float(fabs(Vector3Dot(Vector, Data.NormalSurface) / Vector.Length()));
    
    		return DistanceSuqared / (Cosine * Area);
    	}
    	Vector3 Random(const Vector3& Origin) const override
    	{
    		auto RandomPoint = Vector3(K, ::Random(Y0, Y1), ::Random(Y0, Y1));
    		return RandomPoint - Origin;
    	}
    
    	bool HitTest(const Ray& RayInstance, float T0, float T1, HitData& Data) const override
    	{
    		auto T = (K - RayInstance.LightOrigin.GetX()) / RayInstance.Direction.GetX();
    		if (T < T0 || T > T1)
    		{
    			return false;
    		}
    
    		auto  Y = RayInstance.LightOrigin.GetY() + T * RayInstance.Direction.GetY();
    		auto  Z = RayInstance.LightOrigin.GetZ() + T * RayInstance.Direction.GetZ();
    
    		float OY0;
    		float OY1;
    		float OZ0;
    		float OZ1;
    
    		OY0 = (const float)Y0;
    		OY1 = (const float)Y1;
    		OZ0 = (const float)Z0;
    		OZ1 = (const float)Z1;
    
    		if (Y < OY0 || Y > OY1 || Z < OZ0 || Z > OZ1)
    		{
    			return false;
    		}
    
    		Data.U = (Y - OY0) / (OY1 - OY0);
    		Data.V = (Z - OZ0) / (OZ1 - OZ0);
    		Data.T = T;
    
    		Vector3 OutwardNormal = Vector3(1.f, 0.f, 0.f);
    
    		Data.SetSurfaceNormal(RayInstance, OutwardNormal);
    		Data.Material = Material;
    		Data.RayData = RayInstance.LightAt(T);
    
    		return true;
    	}
    	bool HittingBox(float T0, float T1, BVHBox& BoY) const override
    	{
    		BoY = BVHBox(Vector3(K - 0.0001f, Y0, Z0), Vector3(K + 0.0001f, Y1, Z1));
    
    		return true;
    	}
    
    public:
    	float			Y0;
    	float			Y1;
    	float			Z0;
    	float			Z1;
    	float			K;
    
    	ObjectMaterial* Material;
    };
    class SphereObject : public HitTestBase
    {
    public:
    	SphereObject() : CenterPoint(0.f, 0.f, 0.f), Radius(0.f)
    	{
    	}
    	SphereObject(Vector3 Center, float SphereRadius, ObjectMaterial* ObjectrMaterial)
    		: CenterPoint(Center), Radius(SphereRadius), Material(ObjectrMaterial)
    	{
    	}
    
    	float PDFValue(const Vector3& Origin, const Vector3& Vector) const override
    	{
    		HitData Data;
    		if (!HitTest(Ray(Origin, Vector), 0.001f, Inf, Data))
    		{
    			return 0.f;
    		}
    
    		auto CosThetaMax = sqrt(1.f - Radius * Radius / (CenterPoint - Origin).LengthSquared());
    		auto SolidAngle = 2.f * PI * (1.f - CosThetaMax);
    
    		return 1.f / SolidAngle;
    	}
    	Vector3 Random(const Vector3& Origin) const override
    	{
    		Vector3 Direction = CenterPoint - Origin;
    		auto	DistanceSquared = Direction.LengthSquared();
    
    		ONB		UVW;
    		UVW.BuildFromW(Direction);
    
    		return UVW.Local(Vector3::RandomToSphere(Radius, DistanceSquared));
    	}
    
    	bool HitTest(const Ray& RayInstance, float MinT, float MaxT, HitData& Data) const override
    	{
    		Vector3 RealtivlyPoint = RayInstance.LightOrigin - CenterPoint;
    
    		auto	A = RayInstance.Direction.LengthSquared();
    		auto	HalfB = Vector3Dot(RealtivlyPoint, RayInstance.Direction);
    		auto	C = RealtivlyPoint.LengthSquared() - Radius * Radius;
    		auto	Statment = HalfB * HalfB - A * C;
    
    		if (Statment > 0)
    		{
    			auto Root = sqrt(Statment);
    			auto TempValue = (-HalfB - Root) / A;
    
    			if (TempValue < MaxT && TempValue > MinT)
    			{
    				Data.T = TempValue;
    				Data.RayData = RayInstance.LightAt(Data.T);
    
    				Vector3 OutwardSurface = (Data.RayData - CenterPoint) / Radius;
    
    				Data.Material = Material;
    				Data.SetSurfaceNormal(RayInstance, OutwardSurface);
    
    				return true;
    			}
    			else
    			{
    				TempValue = (-HalfB + Root) / A;
    
    				if (TempValue < MaxT && TempValue > MinT)
    				{
    					Data.T = TempValue;
    					Data.RayData = RayInstance.LightAt(Data.T);
    
    					Vector3 OutwardSurface = (Data.RayData - CenterPoint) / Radius;
    
    					Data.Material = Material;
    					Data.SetSurfaceNormal(RayInstance, OutwardSurface);
    
    					return true;
    				}
    			}
    		}
    
    		return false;
    	}
    	bool HittingBox(float T0, float T1, BVHBox& Box) const override
    	{
    		Box = BVHBox(CenterPoint - Vector3(Radius, Radius, Radius), CenterPoint + Vector3(Radius, Radius, Radius));
    
    		return true;
    	}
    
    public:
    	Vector3			CenterPoint;
    	float			Radius;
    	ObjectMaterial* Material;
    };
    class FlipFace : public HitTestBase
    {
    public:
    	FlipFace(HitTestBase* Object) : ObjectRef(Object)
    	{
    	}
    
    	bool HitTest(const Ray& Instance, float TMin, float TMax, HitData& Data) const override
    	{
    		if (!ObjectRef->HitTest(Instance, TMin, TMax, Data))
    		{
    			return false;
    		}
    
    		Data.FrontFace = !Data.FrontFace;
    		return true;
    	}
    	bool HittingBox(float T0, float T1, BVHBox& Box) const override
    	{
    		return ObjectRef->HittingBox(T0, T1, Box);
    	}
    
    public:
    	HitTestBase* ObjectRef;
    };
    
    typedef class ObjectList : public HitTestBase
    {
    public:
    	ObjectList()
    	{
    	}
    	ObjectList(HitTestBase* Object)
    	{
    		Objects.push_back(Object);
    	}
    
    	void Clear()
    	{
    		Objects.clear();
    	}
    	void PushObject(HitTestBase* Object)
    	{
    		Objects.push_back(Object);
    	}
    
    	float PDFValue(const Vector3& Origin, const Vector3& Vector) const override
    	{
    		auto Weight = 1.f / float(Objects.size());
    		auto Sum = 0.f;
    
    		for (auto Object : Objects)
    		{
    			Sum += Weight * Object->PDFValue(Origin, Vector);
    		}
    
    		return Sum;
    	}
    	Vector3 Random(const Vector3& OriginPoint) const override
    	{
    		if (Objects.size() > 1)
    		{
    			return Objects[rand() % (Objects.size())]->Random(OriginPoint);
    		}
    		else if (!Objects.empty())
    		{
    			return Objects[0]->Random(OriginPoint);
    		}
    	}
    
    	bool HitTest(const Ray& RayInstance, float MinT, float MaxT, HitData& Data) const override
    	{
    		HitData CacheData;
    		bool	AlreadyHit = false;
    		float	ClosestHit = MaxT;
    
    		for (auto& Object : Objects)
    		{
    			if (Object->HitTest(RayInstance, MinT, ClosestHit, CacheData))
    			{
    				AlreadyHit = true;
    				ClosestHit = CacheData.T;
    				Data = CacheData;
    			}
    		}
    
    		return AlreadyHit;
    	}
    	// 用于 BVH 优化的盒型模型
    	bool HittingBox(float T0, float T1, BVHBox& Box) const override
    	{
    		if (Objects.empty())
    		{
    			return false;
    		}
    
    		BVHBox CacheBox;
    		bool   FirstHit = true;
    
    		for (auto& Object : Objects)
    		{
    			/*
    			 * 当一个对象的碰撞想不在该范围的时候，其所有对象也必然不会处于 BVH 中
    			 */
    			if (!Object->HittingBox(T0, T1, Box))
    			{
    				return false;
    			}
    
    			CacheBox = FirstHit ? CacheBox : GetBox(Box, CacheBox);
    			FirstHit = true;
    		}
    
    		return true;
    	}
    
    public:
    	std::vector<HitTestBase*> Objects;
    } TraceWorld;
    
    class Translate : public HitTestBase
    {
    public:
    	Translate(HitTestBase* Object, const Vector3& Displacement) : ObjectRef(Object), PlaceOffset(Displacement)
    	{
    	}
    
    	bool HitTest(const Ray& RayInstance, float TMin, float TMax, HitData& Data) const override
    	{
    		Ray OffsetRay(RayInstance.LightOrigin - PlaceOffset, RayInstance.Direction);
    		if (!ObjectRef->HitTest(OffsetRay, TMin, TMax, Data))
    		{
    			return false;
    		}
    
    		Data.RayData += PlaceOffset;
    		Data.SetSurfaceNormal(OffsetRay, Data.NormalSurface);
    
    		return true;
    	}
    	bool HittingBox(float T0, float T1, BVHBox& Box) const override
    	{
    		if (!ObjectRef->HittingBox(T0, T1, Box))
    		{
    			return false;
    		}
    
    		Box = BVHBox(Box.BoxMin + PlaceOffset, Box.BoxMax + PlaceOffset);
    
    		return true;
    	}
    
    public:
    	HitTestBase* ObjectRef;
    	Vector3		 PlaceOffset;
    };
    class RotateY : public HitTestBase
    {
    public:
    	RotateY(HitTestBase* Object, float RotateAngle)
    	{
    		ObjectRef = Object;
    		auto Radians = DegreesToRadians(RotateAngle);
    		SinTheta = sin(Radians);
    		CosTheta = cos(Radians);
    		HasBox = Object->HittingBox(0.f, 1.f, Box);
    
    		Vector3 Min(Inf, Inf, Inf);
    		Vector3 Max(-Inf, -Inf, -Inf);
    
    		for (float I = 0.f; I < 2.f; I += 1.f)
    		{
    			for (float J = 0.f; J < 2.f; J += 1.f)
    			{
    				for (float K = 0.f; K < 2.f; K += 1.f)
    				{
    					auto	X = I * Box.BoxMax.GetX() + (1.f - I) * Box.BoxMin.GetX();
    					auto	Y = J * Box.BoxMax.GetY() + (1.f - J) * Box.BoxMin.GetY();
    					auto	Z = K * Box.BoxMax.GetZ() + (1.f - K) * Box.BoxMin.GetZ();
    
    					auto	NewX = CosTheta * X + SinTheta * Z;
    					auto	NewZ = -SinTheta * X + CosTheta * Z;
    
    					Vector3 Resever(NewX, Y, NewZ);
    
    					for (float M = 0.f; M < 3.f; M += 1.f)
    					{
    						Min[M] = min(Min[M], Resever[M]);
    						Max[M] = max(Max[M], Resever[M]);
    					}
    				}
    			}
    		}
    
    		Box = BVHBox(Min, Max);
    	}
    	bool HitTest(const Ray& RayInstance, float TMin, float TMax, HitData& Data) const
    	{
    		Vector3 LightOrigin = RayInstance.LightOrigin;
    		Vector3 LightDirection = RayInstance.Direction;
    
    		LightOrigin[0] = CosTheta * RayInstance.LightOrigin[0] - SinTheta * RayInstance.LightOrigin[2];
    		LightOrigin[2] = SinTheta * RayInstance.LightOrigin[0] + CosTheta * RayInstance.LightOrigin[2];
    
    		LightDirection[0] = CosTheta * RayInstance.Direction[0] - SinTheta * RayInstance.Direction[2];
    		LightDirection[2] = SinTheta * RayInstance.Direction[0] + CosTheta * RayInstance.Direction[2];
    
    		Ray RotatedRay(LightOrigin, LightDirection);
    		if (!ObjectRef->HitTest(RotatedRay, TMin, TMax, Data))
    		{
    			return false;
    		}
    
    		Vector3 RayData = Data.RayData;
    		Vector3 SurfaceNormal = Data.NormalSurface;
    
    		RayData[0] = CosTheta * float(Data.RayData[0]) + SinTheta * float(Data.RayData[2]);
    		RayData[2] = -SinTheta * float(Data.RayData[0]) + CosTheta * float(Data.RayData[2]);
    
    		SurfaceNormal[0] = CosTheta * float(Data.NormalSurface[0]) + SinTheta * float(Data.NormalSurface[2]);
    		SurfaceNormal[2] = -SinTheta * float(Data.NormalSurface[0]) + CosTheta * float(Data.NormalSurface[2]);
    
    		Data.RayData = RayData;
    		Data.SetSurfaceNormal(RotatedRay, SurfaceNormal);
    
    		return true;
    	}
    	bool HittingBox(float T0, float T1, BVHBox& OutputBox) const
    	{
    		OutputBox = Box;
    
    		return HasBox;
    	}
    
    public:
    	HitTestBase* ObjectRef;
    	float		 SinTheta;
    	float		 CosTheta;
    	bool		 HasBox;
    	BVHBox		 Box;
    };
    
    class Cube : public HitTestBase
    {
    public:
    	Cube() = default;
    	Cube(const Vector3& Point0, const Vector3& Point1, ObjectMaterial* Material)
    	{
    		BoxMin = Point0;
    		BoxMax = Point1;
    
    		Sides.PushObject(
    			new XYRect(Point0.GetX(), Point1.GetX(), Point0.GetY(), Point1.GetY(), Point1.GetZ(), Material));
    		Sides.PushObject(new FlipFace(
    			new XYRect(Point0.GetX(), Point1.GetX(), Point0.GetY(), Point1.GetY(), Point0.GetZ(), Material)));
    
    		Sides.PushObject(
    			new XZRect(Point0.GetX(), Point1.GetX(), Point0.GetZ(), Point1.GetZ(), Point1.GetY(), Material));
    		Sides.PushObject(new FlipFace(
    			new XZRect(Point0.GetX(), Point1.GetX(), Point0.GetZ(), Point1.GetZ(), Point0.GetY(), Material)));
    
    		Sides.PushObject(
    			new YZRect(Point0.GetY(), Point1.GetY(), Point0.GetZ(), Point1.GetZ(), Point1.GetX(), Material));
    		Sides.PushObject(new FlipFace(
    			new YZRect(Point0.GetY(), Point1.GetY(), Point0.GetZ(), Point1.GetZ(), Point0.GetX(), Material)));
    	}
    	bool HitTest(const Ray& RayInstance, float T0, float T1, HitData& Data) const override
    	{
    		return Sides.HitTest(RayInstance, T0, T1, Data);
    	}
    	bool HittingBox(float T0, float T1, BVHBox& Box) const
    	{
    		Box = BVHBox(BoxMin, BoxMax);
    
    		return true;
    	}
    
    public:
    	Vector3	   BoxMin;
    	Vector3	   BoxMax;
    	TraceWorld Sides;
    };
    
    typedef class BVHRoot : public HitTestBase
    {
    public:
    	bool BoxCompare(const HitTestBase* Left, const HitTestBase* Righ, int Axis)
    	{
    		BVHBox BoxLeft;
    		BVHBox BoxRight;
    
    		return BoxLeft.BoxMin[Axis] < BoxRight.BoxMin[Axis];
    	}
    	bool BoxCompareX(const HitTestBase* Left, const HitTestBase* Right)
    	{
    		return BoxCompare(Left, Right, 0);
    	}
    	bool BoxCompareY(const HitTestBase* Left, const HitTestBase* Right)
    	{
    		return BoxCompare(Left, Right, 1);
    	}
    	bool BoxCompareZ(const HitTestBase* Left, const HitTestBase* Right)
    	{
    		return BoxCompare(Left, Right, 2);
    	}
    
    public:
    	BVHRoot()
    	{
    	}
    	BVHRoot(std::vector<HitTestBase*> Objects, size_t Begin, size_t End, float Time0, float Time1)
    	{
    		int	 Axis = rand() % 2;
    		auto Comparator =
    			(Axis == 0) ? (std::bind(&BVHRoot::BoxCompareX, this, std::placeholders::_1, std::placeholders::_2))
    			: (Axis == 1) ? (std::bind(&BVHRoot::BoxCompareY, this, std::placeholders::_1, std::placeholders::_2))
    			: (std::bind(&BVHRoot::BoxCompareZ, this, std::placeholders::_1, std::placeholders::_2));
    		auto Range = End - Begin;
    
    		if (Range == 1)
    		{
    			RightNode = Objects[Begin];
    			LeftNode = RightNode;
    		}
    		else if (Range == 2)
    		{
    			if (Comparator(Objects[Begin], Objects[Begin + 1]))
    			{
    				LeftNode = Objects[Begin];
    				RightNode = Objects[Begin + 1];
    			}
    			else
    			{
    				LeftNode = Objects[Begin + 1];
    				RightNode = Objects[Begin];
    			}
    		}
    		else
    		{
    			std::sort(Objects.begin() + Begin, Objects.begin() + End, Comparator);
    
    			auto Middle = Begin + Range / 2;
    
    			LeftNode = new BVHNode(Objects, Begin, Middle, Time0, Time1);
    			RightNode = new BVHNode(Objects, Middle, End, Time0, Time1);
    		}
    
    		BVHBox LeftBox;
    		BVHBox RightBox;
    		LeftNode->HittingBox(Time0, Time1, LeftBox);
    		RightNode->HittingBox(Time0, Time1, RightBox);
    
    		Box = GetBox(LeftBox, RightBox);
    	}
    	BVHRoot(TraceWorld& World, float Time0, float Time1)
    		: BVHRoot(World.Objects, 0, World.Objects.size(), Time0, Time1)
    	{
    	}
    
    	bool HitTest(const Ray& RayInstance, float TMin, float TMax, HitData& Data) const override
    	{
    		if (!Box.Hit(RayInstance, TMin, TMax))
    		{
    			return false;
    		}
    
    		bool HitLeft = LeftNode->HitTest(RayInstance, TMin, TMax, Data);
    		bool HitRight = RightNode->HitTest(RayInstance, TMin, HitLeft ? Data.T : TMax, Data);
    
    		return HitLeft || HitRight;
    	}
    	bool HittingBox(float T0, float T1, BVHBox& HitBox) const override
    	{
    		HitBox = Box;
    
    		return true;
    	}
    
    public:
    	HitTestBase* LeftNode;
    	HitTestBase* RightNode;
    
    	BVHBox		 Box;
    
    } BVHNode;
    
    class Camera
    {
    public:
    	static Vector3 RandomInUnitDisk()
    	{
    		while (true)
    		{
    			auto Point = Vector3(Random(-1.f, 1.f), Random(-1.f, 1.f), 0.f);
    			if (Point.LengthSquared() >= 1.f)
    			{
    				continue;
    			}
    
    			return Point;
    		}
    	}
    
    public:
    	Camera(Vector3 LookFrom, Vector3 LookAt, Vector3 VUP, float VFov, float Aspect, float Aperture, float FocusDist)
    	{
    		OriginPoint = LookFrom;
    		LensRadius = Aperture / 2.f;
    
    		auto Theta = DegreesToRadians(VFov);
    		auto ViewHeight = 2.0f * tan(Theta / 2.f);
    		auto ViewWidth = Aspect * ViewHeight;
    
    		W = UnitVector(LookFrom - LookAt);
    		U = UnitVector(Vector3Cross(VUP, W));
    		V = Vector3Cross(W, U);
    
    		Horizontal = FocusDist * ViewWidth * U;
    		Vertical = FocusDist * ViewHeight * V;
    
    		LowerLeftCorner = OriginPoint - Horizontal / 2 - Vertical / 2 - FocusDist * W;
    	}
    	Camera(Vector3 LookFrom, Vector3 LookAt, Vector3 VUP, float VFov, float Aspect)
    		: Camera(LookFrom, LookAt, VUP, VFov, Aspect, 0.f, 1.f)
    	{
    	}
    
    	Ray GetRay(float S, float T)
    	{
    		Vector3 Radius = LensRadius * RandomInUnitDisk();
    		Vector3 Offset = U * Radius.GetX() + V * Radius.GetY();
    
    		return Ray(OriginPoint + Offset, LowerLeftCorner + S * Horizontal + T * Vertical - OriginPoint - Offset);
    	}
    
    public:
    	Vector3 OriginPoint;
    	Vector3 LowerLeftCorner;
    	Vector3 Horizontal;
    	Vector3 Vertical;
    	Vector3 U;
    	Vector3 V;
    	Vector3 W;
    	float	LensRadius;
    };
    
    class MixturePDF : public PDF
    {
    public:
    	MixturePDF(PDF* Object1, PDF* Object2)
    	{
    		PDFList[0] = Object1;
    		PDFList[1] = Object2;
    	}
    
    	float GetValue(const Vector3& Direction) const override
    	{
    		return 0.5f * PDFList[0]->GetValue(Direction) + 0.5f * PDFList[1]->GetValue(Direction);
    	}
    
    	Vector3 Generate() const override
    	{
    		if (Random() < 0.5f)
    		{
    			return PDFList[0]->Generate();
    		}
    		else
    		{
    			return PDFList[1]->Generate();
    		}
    	}
    
    public:
    	PDF* PDFList[2];
    };
    
    /*
     * 检测坏点，已知渲染过程中有概率出现坏点（坏点的 R、G、B 值均为 NaN）
     * 利用 IEE 标准中 NaN != Nan 的方法来判断坏点
     */
    bool IsBadPoint(const Vector3& Vector)
    {
    	auto R = float(Vector[0]);
    	auto G = float(Vector[1]);
    	auto B = float(Vector[2]);
    
    	bool Flag = false;
    
    	if (R != R || G != G || B != B)
    	{
    		Flag = true;
    	}
    
    	return Flag;
    }
    
    DWORD GetRawColor(const Vector3& Vector, float SamplePerPixel)
    {
    	auto R = Vector[0];
    	auto G = Vector[1];
    	auto B = Vector[2];
    
    	if (R != R)
    	{
    		R = 0.f;
    	}
    	if (G != G)
    	{
    		G = 0.f;
    	}
    	if (B != B)
    	{
    		B = 0.f;
    	}
    
    	auto Scale = 1.f / SamplePerPixel;
    
    	R = sqrt(Scale * R);
    	G = sqrt(Scale * G);
    	B = sqrt(Scale * B);
    
    	return RGB(Range(B, 0.f, 0.999f) * 256.f, Range(G, 0.f, 0.999f) * 256.f, Range(R, 0.f, 0.999f) * 256.f);
    }
    
    /*
     * 实际上的渲染是由下到上地渲染，需要镜面反转渲染的点
     */
    template <class Type> Type OppositeCoordinate(const Type& X, const Type& Y, const Type& Width, const Type& Height)
    {
    	return (Height - Y - 1) * Width + X;
    }
    
    TraceWorld GlobalLight;
    /*
     * 渲染函数
     */
    Vector3	   RayColor(const Ray& RayObject, HitTestBase* World, HitTestBase* Light, int Depth)
    {
    	HitData Data;
    
    	if (Depth <= 0)
    	{
    		return Vector3(0.f, 0.f, 0.f);
    	}
    
    	if (!World->HitTest(RayObject, 0.001f, Inf, Data))
    	{
    		return Vector3(0.f, 0.f, 0.f);
    	}
    
    	Ray			ScatteredRay;
    	Vector3		Attenuation;
    	Vector3		Emitted = Data.Material->Emitted(Data.U, Data.V, Data, Data.RayData);
    	float		PDFValue;
    
    	ScatterData ScatterData;
    
    	if (!Data.Material->Scatter(RayObject, Data, ScatterData))
    	{
    		return Emitted;
    	}
    
    	if (ScatterData.IsSpecular)
    	{
    		return ScatterData.Attenuation * RayColor(ScatterData.SpecularRay, World, Light, Depth - 1);
    	}
    
    	auto	   LightPDF = new ObjectHitPDF(&GlobalLight, Data.RayData);
    	MixturePDF MixedPDF(LightPDF, ScatterData.PDF);
    
    	ScatteredRay = Ray(Data.RayData, MixedPDF.Generate());
    	PDFValue = MixedPDF.GetValue(ScatteredRay.Direction);
    
    	delete LightPDF;
    	delete ScatterData.PDF;
    
    	return Emitted + ScatterData.Attenuation * Data.Material->ScatteringPDF(RayObject, Data, ScatteredRay) *
    		RayColor(ScatteredRay, World, Light, Depth - 1) / PDFValue;
    }
    
    TraceWorld	 World;
    BVHRoot* BVHWorld;
    Camera* WorldCamera;
    HitTestBase* RenderWorld;
    
    bool		 RenderStatus[48];
    
    void Render(int StartX, int StartY, int EndX, int EndY, int Width, int Height, int MaxRenderDepth, int SamplePerPixel,
    	const int& ThreadCount)
    {
    	auto Buffer = GetImageBuffer();
    
    	for (int Y = EndY; Y >= StartY; --Y)
    	{
    		for (int X = StartX; X < EndX; ++X)
    		{
    			Vector3 Color;
    
    			while (true)
    			{
    				Color = Vector3(0.f, 0.f, 0.f);
    
    				for (int SampleCount = 0; SampleCount < SamplePerPixel; ++SampleCount)
    				{
    					auto U = (float)(X + Random()) / Width;
    					auto V = (float)(Y + Random()) / Height;
    
    					auto RayInstance = WorldCamera->GetRay(U, V);
    					Color += RayColor(RayInstance, RenderWorld, &GlobalLight, MaxRenderDepth);
    				}
    
    				// 遇到坏点则重新渲染
    				if (!IsBadPoint(Color))
    				{
    					break;
    				}
    			}
    
    			Buffer[OppositeCoordinate(X, Y, Width, Height)] =
    				GetRawColor(Color, float(static_cast<float>(SamplePerPixel)));
    		}
    	}
    
    	RenderStatus[ThreadCount] = true;
    }
    void StartThreadRender(const int& Width, const int& Height, const int& MaxRenderDepth, const int& SamplePerPixel)
    {
    	for (int Count = 0; Count < 48; ++Count)
    	{
    		RenderStatus[Count] = false;
    	}
    
    	auto Count = 0;
    
    	for (int Y = Height - 1; Y >= 0; Y -= 80)
    	{
    		for (int X = 0; X < Width; X += 80)
    		{
    			if (Y - 80 >= 0)
    			{
    				std::thread RendThread(Render, X, Y - 80, X + 80, Y, Width, Height, MaxRenderDepth, SamplePerPixel,
    					Count);
    
    				RendThread.detach();
    			}
    			else
    			{
    				std::thread RendThread(Render, X, Y - 79, X + 80, Y, Width, Height, MaxRenderDepth, SamplePerPixel,
    					Count);
    
    				RendThread.detach();
    			}
    
    			++Count;
    		}
    	}
    
    	while (true)
    	{
    		bool Flag = false;
    
    		for (int Count = 0; Count < 48; ++Count)
    		{
    			if (!RenderStatus[Count])
    			{
    				Flag = true;
    			}
    		}
    
    		if (!Flag)
    		{
    			return;
    		}
    
    		Sleep(4);
    	}
    }
    
    void Room1()
    {
    	Vector3 VUP(0.f, 1.f, 0.f);
    	WorldCamera = new Camera(Vector3(-2.f, 2.f, 1.f), Vector3(0.f, 0.f, -1.f), VUP, 90.f, 640.f / 480.f);
    
    	auto		  Light = new DiffuseLight(Vector3(1.f, 1.f, 1.f));
    	SphereObject* GroudSphere = new SphereObject(Vector3(0.f, -1000.f, 0.f), 1000.f, Light);
    
    	GlobalLight.PushObject(GroudSphere);
    	World.PushObject(GroudSphere);
    
    	int i = 1;
    	for (int CountX = -11; CountX < 11; CountX++)
    	{
    		for (int CountY = -11; CountY < 11; CountY++)
    		{
    			auto	ChooseMat = Random();
    
    			Vector3 Center(static_cast<float>(CountX + 0.9 * Random()), 0.2f,
    				static_cast<float>(CountY + 0.9 * Random()));
    			if ((Center - Vector3(4.f, 0.2f, 0.f)).Length() > 0.9)
    			{
    				if (ChooseMat < 0.8)
    				{
    					auto				Albedo = Vector3::RandomInUnitSphere() * Vector3::RandomInUnitSphere();
    					LambertianMaterial* Material = new LambertianMaterial(new ConstantTexture(Albedo));
    					SphereObject* Sphere = new SphereObject(Center, 0.2f, Material);
    
    					World.PushObject(Sphere);
    				}
    				else if (ChooseMat < 0.95)
    				{
    					auto				Albedo = Ray::RandomRay(.5f, 1.f);
    					auto				Fuzz = Random(0.f, .5f);
    
    					LambertianMaterial* Material = new LambertianMaterial(new ConstantTexture(Albedo));
    					SphereObject* Sphere = new SphereObject(Center, 0.2f, Material);
    
    					World.PushObject(Sphere);
    				}
    				else
    				{
    					DielectricMaterial* Material = new DielectricMaterial(1.5f);
    					SphereObject* Sphere = new SphereObject(Center, 0.2f, Material);
    
    					World.PushObject(Sphere);
    				}
    			}
    		}
    	}
    
    	DielectricMaterial* GlassMaterial = new DielectricMaterial(1.5f);
    	LambertianMaterial* Lambertian = new LambertianMaterial(new ConstantTexture(Vector3(0.4f, 0.2f, 0.1f)));
    	MetalMaterial* Metal = new MetalMaterial(Vector3(0.7f, 0.6f, 0.5f), 0.f);
    
    	SphereObject* Sphere1 = new SphereObject(Vector3(0.f, 1.f, 0.f), 1.f, GlassMaterial);
    	SphereObject* Sphere2 = new SphereObject(Vector3(-4.f, 1.f, 0.f), 1.f, Lambertian);
    	SphereObject* Sphere3 = new SphereObject(Vector3(4.f, 1.f, 0.f), 1.f, Metal);
    
    	World.PushObject(Sphere1);
    	World.PushObject(Sphere2);
    	World.PushObject(Sphere3);
    }
    void Room2()
    {
    	Vector3 VUP(0.f, 1.f, 0.f);
    	WorldCamera = new Camera(Vector3(-2.f, 2.f, 1.f), Vector3(0.f, 0.f, -1.f), VUP, 90.f, 640.f / 480.f);
    
    	auto				Light = new DiffuseLight(Vector3(0.7f, 0.7f, 0.7f));
    
    	LambertianMaterial* Lambertian = new LambertianMaterial(new CheckerTexture(
    		new ConstantTexture(Vector3(1.f, 1.f, 1.f)), new ConstantTexture(Vector3(0.f, 0.f, 0.f))));
    	SphereObject* GroudSphere = new SphereObject(Vector3(0.f, -1000.f, 0.f), 1000.f, Lambertian);
    
    	World.PushObject(GroudSphere);
    
    	int i = 1;
    	for (int CountX = -11; CountX < 11; CountX++)
    	{
    		for (int CountY = -11; CountY < 11; CountY++)
    		{
    			auto	ChooseMat = Random();
    
    			Vector3 Center(static_cast<float>(CountX + 0.9 * Random()), 0.2f,
    				static_cast<float>(CountY + 0.9 * Random()));
    			if ((Center - Vector3(4.f, 0.2f, 0.f)).Length() > 0.9)
    			{
    				if (ChooseMat < 0.8)
    				{
    					auto				Albedo = Vector3::RandomInUnitSphere() * Vector3::RandomInUnitSphere();
    					LambertianMaterial* Material = new LambertianMaterial(new ConstantTexture(Albedo));
    					SphereObject* Sphere = new SphereObject(Center, 0.2f, Material);
    
    					World.PushObject(Sphere);
    				}
    				else if (ChooseMat < 0.95)
    				{
    					auto				Albedo = Ray::RandomRay(.5f, 1.f);
    					auto				Fuzz = Random(0.f, .5f);
    
    					LambertianMaterial* Material = new LambertianMaterial(new ConstantTexture(Albedo));
    					SphereObject* Sphere = new SphereObject(Center, 0.2f, Material);
    
    					World.PushObject(Sphere);
    				}
    				else
    				{
    					SphereObject* Sphere = new SphereObject(Center, 0.2f, Light);
    
    					if (GlobalLight.Objects.empty())
    					{
    						GlobalLight.PushObject(Sphere);
    					}
    					World.PushObject(Sphere);
    				}
    			}
    		}
    	}
    
    	DielectricMaterial* GlassMaterial = new DielectricMaterial(1.5f);
    	MetalMaterial* Metal = new MetalMaterial(Vector3(0.7f, 0.6f, 0.5f), 0.f);
    
    	SphereObject* Sphere1 = new SphereObject(Vector3(0.f, 1.f, 0.f), 1.f, GlassMaterial);
    	SphereObject* Sphere2 = new SphereObject(Vector3(-4.f, 1.f, 0.f), 1.f, Lambertian);
    	SphereObject* Sphere3 = new SphereObject(Vector3(4.f, 1.f, 0.f), 1.f, Metal);
    
    	World.PushObject(Sphere1);
    	World.PushObject(Sphere2);
    	World.PushObject(Sphere3);
    }
    void Room3()
    {
    	Vector3 VUP(0.f, 1.f, 0.f);
    	WorldCamera =
    		new Camera(Vector3(278.f, 278.f, -800.f), Vector3(278.f, 278.f, 0.f), VUP, 40.f, 640.f / 480.f);
    
    	World.Clear();
    
    	auto Red = new LambertianMaterial(new ConstantTexture(Vector3(0.65, 0.05, 0.05)));
    	auto White = new LambertianMaterial(new ConstantTexture(Vector3(0.73, 0.73, 0.73)));
    	auto Metal = new MetalMaterial(Vector3(0.8f, 0.85f, 0.88f), 0.0f);
    	auto Green = new LambertianMaterial(new ConstantTexture(Vector3(0.12, 0.45, 0.15)));
    	auto Light = new DiffuseLight(Vector3(15.f, 15.f, 15.f));
    
    	auto LightObject = new FlipFace(new XZRect(213, 343, 227, 332, 554, Light));
    
    	World.PushObject(new FlipFace(new YZRect(0, 555, 0, 555, 555, Green)));
    	World.PushObject(new YZRect(0, 555, 0, 555, 0, Red));
    	World.PushObject(LightObject);
    	World.PushObject(new FlipFace(new XZRect(0, 555, 0, 555, 555, White)));
    	World.PushObject(new XZRect(0, 555, 0, 555, 0, White));
    	World.PushObject(new FlipFace(new XYRect(0, 555, 0, 555, 555, White)));
    
    	GlobalLight.PushObject(LightObject->ObjectRef);
    
    	World.PushObject(
    		new Translate(new RotateY(new Cube(Vector3(0.f, 0.f, 0.f), Vector3(165.f, 330.f, 165.f), White), 15.f),
    			Vector3(265.f, 0.f, 295.f)));
    	World.PushObject(new Translate(new RotateY(new SphereObject(Vector3(190.f, 90.f, 190.f), 90.f, Metal), -18.f),
    		Vector3(130.f, 0.f, 65.f)));
    }
    void Room4()
    {
    	Vector3 VUP(0.f, 1.f, 0.f);
    	WorldCamera = new Camera(Vector3(478.f, 278.f, -600.f), Vector3(278.f, 278.f, 0.f), VUP, 40.f,
    		640.f / 480.f, 0.f, 10.f);
    
    	auto	  Ground = new LambertianMaterial(new ConstantTexture(Vector3(0.48, 0.83, 0.53)));
    
    	const int BoxesCount = 20.f;
    	for (int I = 0; I < BoxesCount; I++)
    	{
    		for (int J = 0; J < BoxesCount; J++)
    		{
    			auto W = 100.f;
    			auto X0 = -1000.f + float(I) * W;
    			auto Z0 = -1000.f + float(J) * W;
    			auto Y0 = 0.f;
    			auto X1 = X0 + W;
    			auto Y1 = Random(1.f, 101.f);
    			auto Z1 = Z0 + W;
    
    			World.PushObject(new Cube(Vector3(X0, Y0, Z0), Vector3(X1, Y1, Z1), Ground));
    		}
    	}
    
    	World.PushObject(new SphereObject(Vector3(360.f, 150.f, 145.f), 70.f, new DielectricMaterial(1.5f)));
    
    	auto		Light = new DiffuseLight(Vector3(10.f, 10.f, 10.f));
    	auto		LightBox = new FlipFace(new XZRect(123, 423, 147, 412, 550.f, Light));
    
    	TraceWorld* Boxes = new TraceWorld;
    	auto		White = new LambertianMaterial(new ConstantTexture(Vector3(0.73, 0.73, 0.73)));
    	int			SphereCount = 1000;
    	for (int Count = 0; Count < SphereCount; ++Count)
    	{
    		Boxes->PushObject(new SphereObject(Vector3::Random(0.f, 165.f), 10, White));
    	}
    
    	World.PushObject(new SphereObject(Vector3(400.f, 200.f, 400.f), 100.f,
    		new MetalMaterial(Vector3(0.7f, 0.68f, 0.6f), 0.f)));
    	World.PushObject(new Translate(new RotateY(new BVHNode(*Boxes, 0.0f, 1.0f), 15), Vector3(-100, 270, 395)));
    
    	World.PushObject(LightBox);
    	GlobalLight.PushObject(LightBox->ObjectRef);
    }
    
    int main()
    {
    	const int	 Width = 640;
    	const int	 Height = 480;
    	int			 SamplePerPixel = 100;
    	const int	 MaxRenderDepth = 50;
    	bool		 UseMulThreadRender = true;
    	bool		 UseBVH = true;
    	const float AspectRatio = float(Width) / float(Height);
    
    	initgraph(Width, Height, EW_SHOWCONSOLE);
    
    	auto Buffer = GetImageBuffer();
    	int	 RoomNumber = 0;
    
    	while (true)
    	{
    		wchar_t Buffer[2];
    		InputBox(Buffer, 2, L"将要渲染的场景（1~4）");
    
    		if (Buffer[0] == L'1')
    		{
    			RoomNumber = 1;
    
    			Room1();
    			break;
    		}
    		if (Buffer[0] == L'2')
    		{
    			RoomNumber = 2;
    
    			Room2();
    			break;
    		}
    		if (Buffer[0] == L'3')
    		{
    			RoomNumber = 3;
    
    			Room3();
    			break;
    		}
    		if (Buffer[0] == L'4')
    		{
    			RoomNumber = 4;
    
    			Room4();
    			break;
    		}
    	}
    
    	wchar_t SamplePerPixelString[1024];
    	InputBox(SamplePerPixelString, 1024, L"采样率（1～10000），默认 200。采样率越高，效果越好，速度越慢。");
    	SamplePerPixel = _wtoi(SamplePerPixelString);
    
    	if (MessageBox(GetHWnd(), L"启用多线程渲染", L"渲染设置", MB_OKCANCEL) != IDCANCEL)
    	{
    		UseMulThreadRender = true;
    	}
    	else
    	{
    		UseMulThreadRender = false;
    	}
    	if (MessageBox(GetHWnd(), L"启用 BVH", L"渲染设置", MB_OKCANCEL) != IDCANCEL)
    	{
    		UseBVH = true;
    	}
    	else
    	{
    		UseBVH = false;
    	}
    
    	BVHWorld = new BVHRoot(World, 0.f, 1.f);
    
    	if (UseBVH)
    	{
    		RenderWorld = BVHWorld;
    	}
    	else
    	{
    		RenderWorld = &World;
    	}
    
    	time_t Start = clock();
    
    	if (UseMulThreadRender)
    	{
    		StartThreadRender(Width, Height, MaxRenderDepth, SamplePerPixel);
    	}
    	else
    	{
    		auto Buffer = GetImageBuffer();
    
    		for (int Y = Height - 1; Y >= 0; --Y)
    		{
    			for (int X = 0; X < Width; ++X)
    			{
    				Vector3 Color;
    
    				while (true)
    				{
    					Color = Vector3(0.f, 0.f, 0.f);
    
    					for (int SampleCount = 0; SampleCount < SamplePerPixel; ++SampleCount)
    					{
    						auto U = (float)(X + Random()) / Width;
    						auto V = (float)(Y + Random()) / Height;
    
    						auto RayInstance = WorldCamera->GetRay(U, V);
    						Color += RayColor(RayInstance, RenderWorld, &GlobalLight, MaxRenderDepth);
    					}
    
    					// 遇到坏点则重新渲染
    					if (!IsBadPoint(Color))
    					{
    						break;
    					}
    				}
    
    				Buffer[OppositeCoordinate(X, Y, Width, Height)] =
    					GetRawColor(Color, float(static_cast<float>(SamplePerPixel)));
    			}
    		}
    	}
    
    	std::wstringstream StringStream;
    	StringStream << std::setprecision(7) << float(clock() - Start) / 1000.f;
    
    	outtextxy(0, 0, (L"渲染用时：" + StringStream.str() + L" 秒").c_str());
    	outtextxy(0, 20, (L"采样率：" + std::to_wstring(SamplePerPixel) + L" spp").c_str());
    
    	if (UseMulThreadRender)
    	{
    		outtextxy(0, 40, L"多线程渲染：开");
    	}
    	else
    	{
    		outtextxy(0, 40, L"多线程渲染：关");
    	}
    	if (UseBVH)
    	{
    		outtextxy(0, 60, L"BVH 优化：开");
    	}
    	else
    	{
    		outtextxy(0, 60, L"BVH 优化：关");
    	}
    
    	outtextxy(0, 80, (L"Room " + std::to_wstring(RoomNumber)).c_str());
    
    	_getch();
    
    	return 0;
    }
    

# 参考资料

  * [RayTracing in one weekend](https://raytracing.github.io/)
  * [基于光线追踪的三维显示内容生成加速方法研究 - 刘雪](https://cdmd.cnki.com.cn/Article/CDMD-10013-1021103623.htm)
  * [光线追踪软件及分而治之加速算法研究实现 - 虞鸿](https://cdmd.cnki.com.cn/Article/CDMD-10337-1015502679.htm)



[3D](/t/3D)[光线追踪](/t/%E5%85%89%E7%BA%BF%E8%BF%BD%E8%B8%AA)[光追](/t/%E5%85%89%E8%BF%BD)[图形学](/t/%E5%9B%BE%E5%BD%A2%E5%AD%A6)[图形学与图像学](/t/%E5%9B%BE%E5%BD%A2%E5%AD%A6%E4%B8%8E%E5%9B%BE%E5%83%8F%E5%AD%A6)

###  评论 (8) [-](javascript:toggle_visibility\('commentlist', 'commenttoggle'\);)

  * ![](//thirdqq.qlogo.cn/g?b=oidb&k=iaPE8m37fcfQRydKF5FbEGA&kti=ZIrhYwAAAAE&s=640&t=1666351871)

[锦言子书](https://i.codebus.cn/user/078c8087-7686-fb51-57f1-718d3cac8b93)

大大您好，这个代码可以直接运行嘛，还是说需要一些额外的配置，我VScode可以运行那个光栅化球体的程序，但是这个会报很多错orz，应该是缺少很多lib

2023-6-15 \- [回复](javascript:void\(0\);)

    * ![](//thirdqq.qlogo.cn/ek_qqapp/AQXVzdvTjicaAnwD7Seicaz20DWNG9lYFAKhtcHBSfuehMys0wd8eDDqyuXMzHv72CbfA1UjOlPiaUGSKOVOEhgTicGsfwgYXn02OdKDk8Lo8tx8lyQ7YP8/0)

[Margoo](https://i.codebus.cn/user/da13c441-bc12-6521-b7c1-0223f8dc0299)

这个代码只需要有 EasyX 且在 Windows 平台下就可以跑起来了，请问你具体是报的什么错呢？

2023-6-15 \- [回复](javascript:void\(0\);)



  * ![](//thirdqq.qlogo.cn/g?b=oidb&k=iaTTkJrtJ1zkEHGWYWOBKLw&kti=ZLNdXgAAAAE&s=640&t=1674816858)

[侧云](https://i.codebus.cn/user/a8d961df-962d-9716-2579-25d2f99e7b9e)

说是EasyX写光追，实际上只有最后输出图和EasyX有关系，而且是算好2维图给EasyX的

2023-1-31 \- [回复](javascript:void\(0\);)

    * ![](//thirdqq.qlogo.cn/g?b=oidb&k=4V0NyicGge14CnPerjRib0rQ&kti=ZKoSzAAAAAE&s=140&t=1556609814)

[二进制](https://i.codebus.cn/user/69666af9-424d-6885-10df-3e37d79d30a3)

看代码的核心思想，而不是挑骨头！~

2023-6-28 \- [回复](javascript:void\(0\);)

    * ![](//thirdqq.qlogo.cn/ek_qqapp/AQXVzdvTjicaAnwD7Seicaz20DWNG9lYFAKhtcHBSfuehMys0wd8eDDqyuXMzHv72CbfA1UjOlPiaUGSKOVOEhgTicGsfwgYXn02OdKDk8Lo8tx8lyQ7YP8/0)

[Margoo](https://i.codebus.cn/user/da13c441-bc12-6521-b7c1-0223f8dc0299)

EasyX 本身是数学库吗？什么叫算好二维图给 EasyX，我用 OpenGL 的 ComputeShader 写出来不叫 OpenGL 的光追？更何况 EasyX 本身有啥可以用来写光追的

2023-1-31 \- [回复](javascript:void\(0\);)

    * ![](//thirdqq.qlogo.cn/ek_qqapp/AQXVzdvTjicaAnwD7Seicaz20DWNG9lYFAKhtcHBSfuehMys0wd8eDDqyuXMzHv72CbfA1UjOlPiaUGSKOVOEhgTicGsfwgYXn02OdKDk8Lo8tx8lyQ7YP8/0)

[Margoo](https://i.codebus.cn/user/da13c441-bc12-6521-b7c1-0223f8dc0299)

可关键是，这有什么问题吗？

2023-1-31 \- [回复](javascript:void\(0\);)

      * ![](//thirdqq.qlogo.cn/g?b=oidb&k=iaTTkJrtJ1zkEHGWYWOBKLw&kti=ZLNdXgAAAAE&s=640&t=1674816858)

[侧云](https://i.codebus.cn/user/a8d961df-962d-9716-2579-25d2f99e7b9e)

没有问题，我想错了

2023-7-14 \- [回复](javascript:void\(0\);)



  * ![](//thirdqq.qlogo.cn/g?b=oidb&k=4m8Z7BUIibMaBoIumwzxsWw&kti=Y9JwfQAAAAI&s=640&t=1632061631)

[浮芒Friman](https://i.codebus.cn/user/06fe9c4b-6251-2d40-12b8-20f95c876635)

用EasyX写光追？太牛了吧！！！

2023-1-26 \- [回复](javascript:void\(0\);)




![Saving the comment](/Content/images/blog/ajax-loader.gif)

### 添加评论

[](https://i.codebus.cn/account/login?&returnUrl=https://codebus.cn/margoo/a/ray-tracing-plus\))

[取消回复](javascript:void\(0\);)

![Margoo](//thirdqq.qlogo.cn/ek_qqapp/AQXVzdvTjicaAnwD7Seicaz20DWNG9lYFAKhtcHBSfuehMys0wd8eDDqyuXMzHv72CbfA1UjOlPiaUGSKOVOEhgTicGsfwgYXn02OdKDk8Lo8tx8lyQ7YP8/0)

[Margoo](/margoo/)

  * [__](/margoo/ "主页")
  * [__](https://i.codebus.cn/user/da13c441-bc12-6521-b7c1-0223f8dc0299 "用户信息")
  * [ __](tencent://QQInterLive/?Cmd=2&Uin=1683691371 "添加 QQ 好友.") [ __](mqqwpa://im/chat?chat_type=wpa&uin=1683691371&version=1&src_type=web&web_src=https://codebus.cn)
  * [__](https://i.codebus.cn/friend/add?fid=da13c441-bc12-6521-b7c1-0223f8dc0299 "添加好友")



搜索

#### 标签云

  * [刚体](/margoo/t/%E5%88%9A%E4%BD%93)
  * [教程](/margoo/t/%E6%95%99%E7%A8%8B)
  * [EasyX_20210730](/margoo/t/EasyX_20210730)
  * [伪3D](/margoo/t/%E4%BC%AA3D)
  * [游戏](/margoo/t/%E6%B8%B8%E6%88%8F)
  * [PBR](/margoo/t/PBR)
  * [工具](/margoo/t/%E5%B7%A5%E5%85%B7)
  * [光照](/margoo/t/%E5%85%89%E7%85%A7)
  * [LaTeX](/margoo/t/LaTeX)
  * [图形学](/margoo/t/%E5%9B%BE%E5%BD%A2%E5%AD%A6)
  * [函数图像](/margoo/t/%E5%87%BD%E6%95%B0%E5%9B%BE%E5%83%8F)
  * [UI](/margoo/t/UI)
  * [算法](/margoo/t/%E7%AE%97%E6%B3%95)
  * [GUI](/margoo/t/GUI)
  * [EasyX](/margoo/t/EasyX)
  * [3D](/margoo/t/3D)
  * [图形学与图像学](/margoo/t/%E5%9B%BE%E5%BD%A2%E5%AD%A6%E4%B8%8E%E5%9B%BE%E5%83%8F%E5%AD%A6)
  * [光追](/margoo/t/%E5%85%89%E8%BF%BD)
  * [抗锯齿](/margoo/t/%E6%8A%97%E9%94%AF%E9%BD%BF)
  * [光线追踪](/margoo/t/%E5%85%89%E7%BA%BF%E8%BF%BD%E8%B8%AA)



广告

Copyright © 2026 [意在](https://easyx.tech) 投诉举报：admin@easyx.cn

![](https://cdn.easyx.cn/mpsbeian.png) [冀公网安备13010402003013](https://beian.mps.gov.cn/#/query/webSearch?code=13010402003013) [冀ICP备18009530号-3](http://beian.miit.gov.cn)

链接已经复制到剪贴板，可以直接粘贴给需要分享的好友。 
