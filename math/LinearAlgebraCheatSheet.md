# Vector

Magnitude
```CPP
SquareRoot(Square(vector.X) + Square(vector.Y) + Square(vector.Z));
```

Distance
```CPP
SquareRoot(Square(vector1.X - vector2.X) + Square(vector1.Y - vector2.Y) + Square(vector1.Z - vector2.Z));
```

Dot
```CPP
vector1.X * vector2.X + vector1.Y * vector2.Y;
```

Normal
```CPP
vector.X /= mag;
vector.Y /= mag;
vector.Z /= mag;
```

Angle rad
```CPP
acos(Dot(vector1, vector2) / Magnitude(vector1) * Magnitude(vector2));
```

Rad to deg
```CPP
value *= 180 / PI;
```

Deg to rad
```CPP
value *= PI / 180;
```

Rotate rad
```CPP
if (clockwise)
{
    angle = 2 * PI - angle;
}
vector.X = vector.X * cos(angle) - vector.Y * sin(angle);
vector.Y = vector.X * sin(angle) + vector.Y * cos(angle);
```

Cross
```CPP
x = vector1.y * vector2.z - vector1.z * vector2.y;
y = vector1.z * vector2.x - vector1.x * vector2.z;
z = vector1.x * vector2.y - vector1.y * vector2.x;
```

Look at
```CPP
dir = focusPoint - vector;
angle = Angle(forward, dir);

clockwise = false;
if(Cross(forward, dir).z < 0)
{
    clockwise = true;
}
Rotate();
```

Translate
```CPP
position.x + vector.x;
position.y + vector.y;
position.z + vector.z;
```



# Matrices
## 3D rotation about axis matrices

```
Matrix4x4 rotationX = new Matrix4x4(
                      new Vector4(1, 0,                     0,                0),
                      new Vector4(0, Mathf.Cos(angle)     , Mathf.Sin(angle), 0),
                      new Vector4(0, -1 * Mathf.Sin(angle), Mathf.Cos(angle), 0),
                      new Vector4(0, 0,                     0,                1));
``` 
```
Matrix4x4 rotationY = new Matrix4x4(
                      new Vector4(Mathf.Cos(angle), 0, -1 * Mathf.Sin(angle), 0),
                      new Vector4(0,                1, 0,                     0),
                      new Vector4(Mathf.Sin(angle), 0, Mathf.Cos(angle),      0),
                      new Vector4(0,                0, 0,                     1));
``` 
```
Matrix4x4 rotationZ = new Matrix4x4(
                      new Vector4(Mathf.Cos(angle),      Mathf.Sin(angle), 0, 0),
                      new Vector4(-1 * Mathf.Sin(angle), Mathf.Cos(angle), 0, 0),
                      new Vector4(0,                     0,                1, 0),
                      new Vector4(0,                     0,                0, 1));
``` 
