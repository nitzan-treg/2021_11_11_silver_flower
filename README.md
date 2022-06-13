#  Silver Flower
In this project I tried to create a procedural feather, furethermore (pun intended) I wanted to do it all in vex without any grooming tools.

<img src="Images/silver_flower.png">
<img alt = "gif" src="Images/feather_vex.gif">

I would calculate the tangent to the curve and rotate it toward the required direction, then I would control the angle and length of the feather with parameters that are drived from the following code:

```c++
vector pos = v@P;
float u = f@curveu;
vector dir = v@tangentu;
vector up = chv('up');
f@curveu2 = 0;

//fix up
vector bi = cross(dir,up);
up = cross(bi,dir);

//Find Left and Right directions
float angle = chramp('rotate_ramp',u);
angle = fit01(angle,chf('min_angle'),chf('max_angle'))*PI;


matrix3 rot = ident();
rotate(rot,angle,up);

vector L_dir = normalize(dir*rot);
vector R_dir = normalize(dir*invert(rot));

//Find line Length
float length = chramp('length_ramp',u);
length = fit01(length,chf('min_length'),chf('max_length'));

//find ends pos
vector end_pos0 = v@P+L_dir*length*f@pscale;
vector end_pos1 = v@P+R_dir*length*f@pscale;

//create segements
int prim0 = addprim(0,'polyline');
int prim1 = addprim(0,'polyline');

int segments = chi('segments');
for(int seg=0;seg<segments;seg++){
    float ratio = float(seg)/float(segments-1);
    vector npos0 = lerp(pos,end_pos0,ratio);
    vector npos1 = lerp(pos,end_pos1,ratio);
    
    int npt0 = addpoint(0,npos0);
    int npt1 = addpoint(0,npos1);
    
    addvertex(0,prim0,npt0);
    addvertex(0,prim1,npt1);
    
    setpointattrib(0,'curveu2',npt0,ratio,'set');
    setpointattrib(0,'curveu2',npt1,ratio,'set');
}

```

Node Tree:
<img src="Images/Node Tree.png">