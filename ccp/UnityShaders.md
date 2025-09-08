# Structure

## Fragment Vertex
In the Vertex Fragment shader, the vertex is used to project coordinates of the model to the screen.

```HLSL
Shader "Unlit"
{
    Properties { }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        LOD 100

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #include "UnityCG.cginc"

            struct appdata { };

            struct v2f { };

            v2f vert (appdata v) { }

            fixed4 frag (v2f i) : SV_Target { }
            ENDCG
        }
    }
}
```

## Standard Surface

```HLSL
Shader "StandardSurface"
{
    Properties { }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        LOD 200

        CGPROGRAM
        #pragma surface surf Standard fullforwardshadows
        #pragma target 3.0

        struct Input { };
        
        void surf (Input IN, inout SurfaceOutputStandard o) { }
        ENDCG
    }
    FallBack "Diffuse"
}
```

## Surface Vertex
Vertex function in Surface Shader is used to alter the geometry.
```HLSL
Shader "SrufaceVertex"
{
    Properties { }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        LOD 200

        CGPROGRAM
        #pragma surface surf Lambert vertex:vert nolightmap

        struct Input { };

        void vert(inout appdata_full v) { }

        void surf (Input IN, inout SurfaceOutput o) { }
        ENDCG
    }
    FallBack "Diffuse"
}
```

## Multipass Shader Format
```HLSL
Shader "MultipassShader"
{
    Properties { }
    SubShader  
    {                            
        //FIRST PASS - SURFACE SHADER DOES NOT REQUIRE PASS BLOCK
        Tags { "Queue" = "Geometry+1" }
 
        CGPROGRAM
        #pragma surface surf BlinnPhong 

        struct Input { };
       
        void surf (Input IN, inout SurfaceOutput o) { }
        ENDCG
 
        //SECOND PASS - ANOTHER SURFACE SHADER, NO PASS BLOCK REQUIRED
        ZWrite Off      
        Blend DstColor Zero
        CGPROGRAM
        
        #pragma surface surf BlinnPhong
        struct Input { };
     
        void surf (Input IN, inout SurfaceOutput o) { }
        ENDCG  
 
        //THIRD PASS -  VERT/FRAG NEEDS TO BE ENCLOSED IN PASS
        Pass
        {
            Tags { "LightMode" = "Always" }
            ZWrite Off
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            struct v2f { };
            v2f vert (appdata_full v) { }
                        
            half4 frag( v2f i ) : COLOR { }
            ENDCG          
        }
       
        //FOURTH PASS - SIMPLE SHADER LAB FUNCTIONS
        Pass
        {          
            Tags { "LightMode" = "Always" }
            ZWrite Off
            SetTexture [_MainTex]
            {
                 combine constant* texture
            }
        } 
    }
    Fallback "Diffuse
}
```
# Variable declarations
Property block which is exposed in inspector on the material

```HLSL
Properties
{
    myColor ("Color", Color) = (1,1,1,1)
    myRange ("Range", Range(0,5)) = 1         //makes a slider
    myTexture ("Texture", 2D) = "white" {}   //"bump" for normal maps
    myCube ("Cubemap", CUBE) = "" {}         //used for skyboxes mainly
    myFloat ("Float", Float) = 0.5
    myVector ("Vector", Vector) = (0.5,1,1,1) //can't be 2 or 3 values, require work around with custom editors
}
```
Variables in CG

**Names have to match from properties!**
```HLSL
fixed4 myColor;
half myRange;        //can be float or fixed too
sampler2D myTex;
samplerCUBE myCube;
float myFloat;
float4 myVector;     //can declare as float2 or float3 but that will not effect inspector view
```
### Variable sizes

`Float` A float is a full 32-bit precision value and is the slowest of the three 
different types we see here. It also has its corresponding values of float2, float3, 
and float4.

`Half` The half variable type is a reduced 16-bit floating point value and is suitable to 
store UV values and color values and is much faster than using a float value. It has its 
corresponding values like the float type, which are half2, half3, and half4.

`Fixed` A fixed value is the smallest in size of the three types, but can be used for 
lighting calculations and colors and has the corresponding values of fixed2, 
fixed3, and fixed4.

### Vector access
You can access color or vector with xyz or rgb it doesn't matter which you prefer

```HLSL
float4 myVector;
myVector.x = myVector.r;
myVector.y = myVector.g;
myVector.z = myVector.b;
myVector.w = myVector.a;
```

Multiple variables
```HLSL
myVector.xy;
myVector.xz;
myVector.gb;
myVector.bgb;

myVector.xyzw;
myVector.rgba;
//cant be myVector.xgbw or similar
```

# Semantics

##  Lambert and BlinnPhong

```HLSL
struct SurfaceOutput
{
    fixed3 Albedo;  // diffuse color
    fixed3 Normal;  // tangent space normal, if written
    fixed3 Emission;
    half Specular;  // specular power in 0..1 range
    fixed Gloss;    // specular intensity
    fixed Alpha;    // alpha for transparencies
};
```

## Standard
```HLSL
struct SurfaceOutputStandard
{
    fixed3 Albedo;      // base (diffuse or specular) color
    fixed3 Normal;      // tangent space normal, if written
    half3 Emission;
    half Metallic;      // 0=non-metal, 1=metal
    half Smoothness;    // 0=rough, 1=smooth
    half Occlusion;     // occlusion (default 1)
    fixed Alpha;        // alpha for transparencies
};
```
## Standard Specular
```HLSL
struct SurfaceOutputStandardSpecular
{
    fixed3 Albedo;      // diffuse color
    fixed3 Specular;    // specular color
    fixed3 Normal;      // tangent space normal, if written
    half3 Emission;
    half Smoothness;    // 0=rough, 1=smooth
    half Occlusion;     // occlusion (default 1)
    fixed Alpha;        // alpha for transparencies
};
```
## Vertex/Fragment Structures
AppData
```HLSL
struct appdata_full {
    float4 vertex : POSITION;       //vertex xyz position
    float4 tangent : TANGENT; 
    float3 normal : NORMAL;
    float4 texcoord : TEXCOORD0;    //uv coordinate for first set of UVs
    float4 texcoord1 : TEXCOORD1;   //uv coordinate for second set of UVs
    float4 texcoord2 : TEXCOORD2;   //uv coordinate for third set of UVs
    float4 texcoord3 : TEXCOORD3;   //uv coordinate for fourth set of UVs
    fixed4 color : COLOR;           //per-vertex colour
};
struct v2f
{
    float4 pos :  SV_POSITION;      //The position of the vertex in clipping space.
    float3 normal : NORMAL;         //The normal of the vertex in clipping space.
    float4 uv : TEXCOORD0;          //UV from first UV set.
    float4 textcoord1 : TEXCOORD1;  //UV from second UV set.
    float4 tangent : TANGENT;       //A vector that runs at right angles to a normal.
    float4 diff : COLOR0;           //Diffuse vertex colour.
    float4 spec : COLOR1;           //Specular vertex colour.
}
```

## Time values

`_Time`	float4 Time since level load (t/20, t, t*2, t*3), use to animate things inside the shaders.

`_SinTime` float4 Sine of time: (t/8, t/4, t/2, t).

`_CosTime` float4 Cosine of time: (t/8, t/4, t/2, t).

`unity_DeltaTime` float4 Delta time: (dt, 1/dt, smoothDt, 1/smoothDt).



# Lighting model implimentations

## Lambert

```HLSL
//Dot product of light direction and surface normal I = N * L
half NdotL = dot(o.Normal, lightDir);

//Return the color
half4 color;
color.rgb = o.Albedo * _LightColor0.rgb * (NdotL * atten);
color.a = o.Alpha;
return color;
```

## Toon

Just add one line to the Lambert

Requires grayscale ramp image

```HLSL
//Remap NdotL to the value of the ramp map
NdotL = text2D(RampTex. fixed2(NdotL, 0.5));
```
Can be also made using a slider with values

```HLSL
//Snap instead
half NdotL = floor(NdotL * CelShadingLevels) / (CelShadingLevels - 0.5);
```

## Phong specular

Properties:

```HLSL
DiffuseColor ("DiffuseColor ", Color) = (1,1,1,1)
MainTex ("MainTex", 2D) = "white" {}

SpecColor ("SpecColor", Color) = (1,1,1,1)
SpecPower ("SpecPower", Range(0, 30)) = 1
```
Function:

```HLSL
//Diffuse I = N * L
half NdotL = dot(o.Normal, lightDir);

//Reflection R = 2N * (N * L) - L
float3 reflection = normalize(2.0 * o.Normal * NdotL - lightDir);

//Specular S = (R*V)^p
float spec = pow(max(0, dot(reflection, viewDir)), SpecPower);
float3 finalSpec = SpecColor.rgb * spec;

//Return color I = N * L + (R * V)^p
half4 color;
color.rgb = o.Albedo * _LightColor0.rgb * max(0, NdotL) * atten + _LightColor0.rgb * finalSpec;
color.a = o.Alpha;
return color;
```

## Blinn-Phong

Same properties as Phong

```HLSL
//Diffuse max
half NdotL = max(0, dot(o.Normal, lightDir));

//Half vector H * (V + L) / (V + L)
float3 halfVector = normalize(lightDir + viewDir);

//Specular S = (N * H)^p
float NdotH = max(0, dot(o.Normal, halfVector));
float spec = pow(NdotH, SpecPower) * SpecColor;

//Return color I = N * L + (N * H)^p
half4 color;
color.rgb = o.Albedo * _LightColor0.rgb * NdotL + (_LightColor0.rgb * SpecColor * spec) * atten;
color.a = o.Alpha;
return color;
```

# Transparency

Set up
```HLSL
Tags 
{
    "RenderType" = "Transparent" 
    "Queue" = "Transparent"
    "IgnoreProjector" = "True"
}

//Not render back
Pass 
{
    ZWrite On
    ColorMask 0
}

//Add to pragma
#pragma surface surf Standard alpha:fade
```

Render queue
```HLSL
Background	This render queue is rendered before any others.		1000
Geometry	Opaque geometry uses this queue.				2000
AlphaTest	Alpha tested geometry uses this queue.				2450
GeometryLast	Last render queue that is considered "opaque".			?
Transparent	Rendered after Geometry and AlphaTest, in back-to-front order.	3000
Overlay		Tmeant for overlay effects.					4000
```

## Blend types

Equation
```HLSL
finalValue = sourceFactor * sourceValue operation destinationFactor * destinationValue
```

Valid parameter values
```HLSL
Integer, range 0,7 	The render target index.
Off			Disables blending.
One			The value of this input is one. Use this to use the value of the source or the destination color.
Zero			The value of this input is zero. Use this to remove either the source or the destination values.
SrcColor		The GPU multiplies the value of this input by the source color value.
SrcAlpha		The GPU multiplies the value of this input by the source alpha value.
DstColor		The GPU multiplies the value of this input by the frame buffer source color value.
DstAlpha		The GPU multiplies the value of this input by the frame buffer source alpha value.
OneMinusSrcColor	The GPU multiplies the value of this input by (1 - source color).
OneMinusSrcAlpha	The GPU multiplies the value of this input by (1 - source alpha).
OneMinusDstColor	The GPU multiplies the value of this input by (1 - destination color).
OneMinusDstAlpha	The GPU multiplies the value of this input by (1 - destination alpha).
```
Common blend types
```HLSL
Blend SrcAlpha OneMinusSrcAlpha // Traditional transparency
Blend One OneMinusSrcAlpha // Premultiplied transparency
Blend One One // Additive
Blend OneMinusDstColor One // Soft additive
Blend DstColor Zero // Multiplicative
Blend DstColor SrcColor // 2x multiplicative
```
Not so common blend types
```HLSL
Blend OneMinusSrcColor OneMinusSrcColor //Negative (I think)
```

# Some random effects

## Vertex

### Extrusion

Will have issues with vertices that are hard-edged and make them separate

Scaling would be a cheaper option

Vertex:
```HLSL
v.vertex.xyz += v.normal * Amount;
```

# Polar shapes

## Signed distance fields

Fragment: 
```HLSL
float sdf = length(abs(i.uv) - 0.5);

//Output colour
float t = step(sdf, Size);
fixed4 col = lerp(ColorA, ColorB, t);
return col;
```

## Polar coordinates conversion 
Vertex: 
```HLSL 
o.uv = v.uv * 2 - 1;
```
Fragment: 
```HLSL
float r = UNITY_TWO_PI;
float a = atan2(i.uv.x , i.uv.y);
float t = ShapeFunction(a);

//outputing colored shape                
float3 col = 1 - smoothstep(t, t, r);
col = lerp(ColorA, ColorB, col);
return fixed4(col, 1);
```
## Shape functions
```HLSL
y = cos(x * 3.0);
y = abs(cos(x * 3.0));
y = abs(cos(x * 2.5)) * 0.5 + 0.3;
y = abs(cos(x * 12.) * sin(x * 3.0)) * 0.8 + 0.1;
y = smoothstep(-0.5, 1.0, cos(x * 10.0)) * 0.2 + 0.5;
```

## Polygon functions

```HLSL
float r = UNITY_TWO_PI / float(Size);
float a = atan2(i.uv.x , i.uv.y) + UNITY_PI;

float t = cos(floor(0.5f + a / r) * r - a) * length(i.uv);
float3 col = 1 - smoothstep(0.4, 0.4, t);
col = lerp(ColorA, ColorB, col);
return fixed4(col, 1);
```

# Collection of HLSL function

`clamp( x , a , b )`
x clamped to the range [a,b]: a if x<a, b if x>b else returns x.

`saturate( x )`
Clamps x to [0, 1].

`step( a , x )`
0 if x<a, 1 if x>=a

`smoothstep( min , max , x )`
For values of x between min and max , returns a smoothly varying value that ranges from 0 at x==min to 1 at x==max. x is clamped to the range [min,max] and then returns 2*((x-min)/(max-min))3+3*((x-min)/(max-min))2

`sign( x )`
Sign of x (returns -1, 0 or +1)

`frac( x )`
Fractional part of x.

`fmod( x , y )`
Remainder of x/y (with the same sign as x). If y==0, result is undefined.

`max( a , b )`
Maximum of a and b .

`min( a , b )`
Minimum of a and b .

`frexp( x , out exp )`
Splits x into normalized fraction in the interval [, 1), which is returned, and a power of 2, which is stored in exp . If x==0, both parts of the result are 0.

`modf( x , out ip )`
Splits x into int and frac parts (each with the same sign as x). Stores the int part in ip and returns the fractional part.

`round( x )`
Closest integer to x.

`ceil( x )`
Smallest integer not less than x.

`floor( x )`
Largest integer not greater than x.

## Algorithmic functions

`abs( x )`
Absolute value of x.

`pow( x, y )`
xy.

`exp( x )`
Exponential func: ex.

`exp2( x )`
Exponential function 2x.

`ldexp( x , n )`
x * 2n.

`sqrt( x )`
Square root of x (x>0)

`rsqrt( x )`
Reciprocal square root of x (x>0).

`log( x )`
Natural logarithm (x>0).

`log2( x )`
Log Base 2 (x>0).

`log10( x )`
Log Base 10 (x>0).

`noise( x )`
Noise function. The returned value is between 0 and 1, and is always the same for a given input value.

## Trigonometric functions

`degrees( x )`
convert radians to degrees.

`radians( x )`
Converts degrees to radians.

`sin( x )`
Sin of x.

`asin( x )`
Arcsin of x in range [-pi/2,pi/2]; x in [-1, 1].
tan( x )
Tan of x.

`sinh( x )`
Hyperbolic sine of x.

`cos( x )`
Cos of x.

`acos( x )`
Arccos of x in range [0, pi], x in [-1, 1].

`cosh( x )`
Hyperbolic cos of x.

`sincos(float x , out s , out c )`
s is set to sin(x), and c to cos(x).

`tanh( x )`
Hyperbolic tan of x.

`atan( x )`
Arctan of x in range [-pi/2, pi/2].

`atan2( y , x )`
Arctan y/x in range [-pi,pi].

## Linear algebra

`normalize( v )`
Normalize v.

`length( v )`
Pythagorean length of vector.

`distance( p1 , p2 )`
Pythagorean distance between p1 and p2.

`dot( A, B )`
Dot product of A and B.

`cross( A , B )`
Cross product of vectors A and B (A and B must be three-component).

`lerp( a , b , f )`
Linear interpolation: (1 - f)* a + b * f. f can be a vector of same length as a & b.

`reflect( I , N )`
Computes reflection of I in a plane with surface normal N. (three-component vectors only).

`refract( I , N , eta )`
Computes refraction of I in a plane with surface normal N and refractive index eta. Returns (0,0,0) for total internal reflection. (three-component vectors only).

`faceforward( N, I, Ng )`
N if dot( Ng , I ) < 0 otherwise (-N).

## Matrices

`transpose( M )`
transpose of matrix M

`determinant( x )`
Determinant of matrix x.

`mul( M , N )`
Matrix product of matrix M and matrix N: If M has size AxB, and N has size BxC, returns an AxC.

`mul( M , v )`
Product of matrix M and column vector v: If M has size AxB, and v is Bx1, returns an Ax1.

`mul( v , M )`
Product of row vector v and matrix M: If v is a 1xA and M is AxB, returns a 1xB.

`lit( NdotL , NdotH , m )`
Computes lighting coefficients (m is the shininess).
Returns: float4: .x=ambient (always 1), .y=diffuse (0 if N.L < 0), .z=specular (0 if N.L<0 or N.H<0); .w==1.0.

## Calculus

`ddx( a )`
Partial derivative of a with respect to screen-space x coordinate

`ddy( a )`
Partial derivative of a with respect to screen-space y coordinate

## Logic

`all( x )`
true if all components of x != 0 - false otherwise.

`any( x )`
true if any component of x != 0 - false otherwise.

`isfinite( x )`
Returns true if x is finite.

`isinf( x )`
Returns true if x is infinite.

`isnan( x )`
Returns true if x is NaN.

`debug(float4 x)`
If compiler DEBUG option is enabled, causes shader to halt with x copied to the COLOR output - otherwise it does nothing.

## Textures

tex1D(sampler1D tex , float s )
1D nonprojective texture lookup

tex1D(sampler1D tex , float s , float dsdx , float dsdy )
1D nonprojective texture lookup with derivatives

tex1D(sampler1D tex , float2 sz )
1D nonprojective depth compare texture lookup

tex1D(sampler1D tex , float2 sz , float dsdx , float dsdy )
1D nonprojective depth compare texture lookup with derivatives

tex1Dproj(sampler1D tex , float2 sq )
1D projective texture lookup

tex1Dproj(sampler1D tex , float3 szq )
1D projective depth compare texture lookup

tex2D(sampler2D tex , float2 s )
2D nonprojective texture lookup

tex2D(sampler2D tex , float2 s , float2 dsdx , float2 dsdy )
2D nonprojective texture lookup with derivatives

tex2D(sampler2D tex , float3 sz )
2D nonprojective depth compare texture lookup

tex2D(sampler2D tex , float3 sz , float2 dsdx , float2 dsdy )
2D nonprojective depth compare texture lookup with derivatives

tex2Dproj(sampler2D tex , float3 sq )
2D projective texture lookup

tex2Dproj(sampler2D tex , float4 szq )
2D projective depth compare texture lookup

texRECT(samplerRECT tex , float2 s )
2D nonprojective texture rectangle texture lookup (OpenGL)

texRECT(samplerRECT tex , float2 s , float2 dsdx , float2 dsdy )
2D nonprojective texture rectangle texture lookup with derivatives (OpenGL)

texRECT(samplerRECT tex , float3 sz )
2D nonprojective texture rectangle depth compare texture lookup (OpenGL)

texRECT(samplerRECT tex , float3 sz , float2 dsdx , float2 dsdy )
2D nonprojective depth compare texture lookup with derivatives (OpenGL)

texRECTproj(samplerRECT tex , float3 sq )
2D texture rectangle projective texture lookup (OpenGL)

texRECTproj(samplerRECT tex , float3 szq )
2D texture rectangle projective depth compare texture lookup (OpenGL)

tex3D(sampler3D tex , float3 s )
3D nonprojective texture lookup

tex3D(sampler3D tex , float3 s , float3 dsdx , float3 dsdy )
3D nonprojective texture lookup with derivatives

tex3Dproj(sampler3D tex , float4 sq )
3D projective texture lookup

texCUBE(samplerCUBE tex , float3 s )
Cubemap nonprojective texture lookup

texCUBE(samplerCUBE tex , float3 s , float3 dsdx , float3 dsdy )
Cubemap nonprojective texture lookup with derivatives

texCUBEproj(samplerCUBE tex , float4 sq )
Cube map projective texture lookup (ignores q)

## Notes

s indicates a one-, two-, or three-component texture coordinate.
z indicates a depth comparison value for shadow map lookups.
q indicates a perspective value, and is used to divide the texture coordinate ( s ) before the texture lookup is performed.
When you use the texture functions that allow specifying a depth comparison value, the associated texture unit must be configured for depth-compare texturing. Otherwise, no depth comparison will actually be performed.

Reference: https://www.sjbaker.org/wiki/index.php?title=Concise_Cg_built-in_function_table
