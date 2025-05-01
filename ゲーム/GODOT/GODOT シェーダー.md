## Realistic CRT shader
https://godotshaders.com/shader/realistic-crt-shader/
![[シェーダー-1746096600599.png|640x357]]
```python
/*
Shader from Godot Shaders - the free shader library.

This shader is under CC0 license. Feel free to use, improve and 
change this shader according to your needs and consider sharing 
the modified result to godotshaders.com.

Optimised and packed by @c64cosmin
If you do use this please share it with me
Would love to see what you're making with it <3

It's a combination of these two shaders
~godotshaders.com/shader/VHS-and-CRT-monitor-effect
godotshaders.com/shader/crt-shader-with-realistic-blurring/

CRT grille and rolling lines made by @c64cosmin
Vignette and warping effect was made by pend00
Scanlines are from "TimothyLottes" FROM SHADERTOY
Then ported by AHOPNESS (@ahopness)
https://www.shadertoy.com/view/MsjXzh
*/

shader_type canvas_item;

uniform sampler2D SCREEN_TEXTURE: hint_screen_texture;

uniform vec2 resolution = vec2(320.0, 180.0);

uniform float scan_line_amount :hint_range(0.0, 1.0) = 1.0;
uniform float warp_amount :hint_range(0.0, 5.0) = 0.1;
uniform float noise_amount :hint_range(0.0, 0.3) = 0.03;
uniform float interference_amount :hint_range(0.0, 1.0) = 0.2;
uniform float grille_amount :hint_range(0.0, 1.0) = 0.1;
uniform float grille_size :hint_range(1.0, 5.0) = 1.0;
uniform float vignette_amount :hint_range(0.0, 2.0) = 0.6;
uniform float vignette_intensity : hint_range(0.0, 1.0) = 0.4;
uniform float aberation_amount :hint_range(0.0, 1.0) = 0.5;
uniform float roll_line_amount :hint_range(0.0, 1.0) = 0.3;
uniform float roll_speed :hint_range(-8.0, 8.0) = 1.0;
uniform float scan_line_strength :hint_range(-12.0, -1.0) = -8.0;
uniform float pixel_strength :hint_range(-4.0, 0.0) = -2.0;

float random(vec2 uv){
    return fract(cos(uv.x * 83.4827 + uv.y * 92.2842) * 43758.5453123);
}

vec3 fetch_pixel(vec2 uv, vec2 off){
	vec2 pos = floor(uv * resolution + off) / resolution + vec2(0.5) / resolution;

	float noise = 0.0;
	if(noise_amount > 0.0){
		noise = random(pos + fract(TIME)) * noise_amount;
	}

	if(max(abs(pos.x - 0.5), abs(pos.y - 0.5)) > 0.5){
		return vec3(0.0, 0.0, 0.0);
	}

	vec3 clr = texture(SCREEN_TEXTURE , pos, -16.0).rgb + noise;
	return clr;
}

// Distance in emulated pixels to nearest texel.
vec2 Dist(vec2 pos){ 
	pos = pos * resolution;
	return - ((pos - floor(pos)) - vec2(0.5));
}
    
// 1D Gaussian.
float Gaus(float pos, float scale){ return exp2(scale * pos * pos); }

// 3-tap Gaussian filter along horz line.
vec3 Horz3(vec2 pos, float off){
	vec3 b = fetch_pixel(pos, vec2(-1.0, off));
	vec3 c = fetch_pixel(pos, vec2( 0.0, off));
	vec3 d = fetch_pixel(pos, vec2( 1.0, off));
	float dst = Dist(pos).x;
	
	// Convert distance to weight.
	float scale = pixel_strength;
	float wb = Gaus(dst - 1.0, scale);
	float wc = Gaus(dst + 0.0, scale);
	float wd = Gaus(dst + 1.0, scale);
	
	// Return filtered sample.
	return (b * wb + c * wc + d * wd) / (wb + wc + wd);
}

// Return scanline weight.
float Scan(vec2 pos, float off){
	float dst = Dist(pos).y;
	
	return Gaus(dst + off, scan_line_strength);
}

// Allow nearest three lines to effect pixel.
vec3 Tri(vec2 pos){
	vec3 clr = fetch_pixel(pos, vec2(0.0));
	if(scan_line_amount > 0.0){
		vec3 a = Horz3(pos,-1.0);
		vec3 b = Horz3(pos, 0.0);
		vec3 c = Horz3(pos, 1.0);

		float wa = Scan(pos,-1.0);
		float wb = Scan(pos, 0.0);
		float wc = Scan(pos, 1.0);

		vec3 scanlines = a * wa + b * wb + c * wc;
		clr = mix(clr, scanlines, scan_line_amount);
	}
	return clr;
}

// Takes in the UV and warps the edges, creating the spherized effect
vec2 warp(vec2 uv){
	vec2 delta = uv - 0.5;
	float delta2 = dot(delta.xy, delta.xy);
	float delta4 = delta2 * delta2;
	float delta_offset = delta4 * warp_amount;
	
	vec2 warped = uv + delta * delta_offset;
	return (warped - 0.5) / mix(1.0,1.2,warp_amount/5.0) + 0.5;
}

float vignette(vec2 uv){
	uv *= 1.0 - uv.xy;
	float vignette = uv.x * uv.y * 15.0;
	return pow(vignette, vignette_intensity * vignette_amount);
}

float floating_mod(float a, float b){
	return a - b * floor(a/b);
}

vec3 grille(vec2 uv){
	float unit = PI / 3.0;
	float scale = 2.0*unit/grille_size;
	float r = smoothstep(0.5, 0.8, cos(uv.x*scale - unit));
	float g = smoothstep(0.5, 0.8, cos(uv.x*scale + unit));
	float b = smoothstep(0.5, 0.8, cos(uv.x*scale + 3.0*unit));
	return mix(vec3(1.0), vec3(r,g,b), grille_amount);
}

float roll_line(vec2 uv){
	float x = uv.y * 3.0 - TIME * roll_speed;
	float f = cos(x) * cos(x * 2.35 + 1.1) * cos(x * 4.45 + 2.3);
	float roll_line = smoothstep(0.5, 0.9, f);
	return roll_line * roll_line_amount;
}

void fragment(){
	vec2 pix = FRAGCOORD.xy;
	vec2 pos = warp(SCREEN_UV);
	
	float line = 0.0;
	if(roll_line_amount > 0.0){
		line = roll_line(pos);
	}

	vec2 sq_pix = floor(pos * resolution) / resolution + vec2(0.5) / resolution;
	if(interference_amount + roll_line_amount > 0.0){
		float interference = random(sq_pix.yy + fract(TIME));
		pos.x += (interference * (interference_amount + line * 6.0)) / resolution.x;
	}

	vec3 clr = Tri(pos);
	if(aberation_amount > 0.0){
		float chromatic = aberation_amount + line * 2.0;
		vec2 chromatic_x = vec2(chromatic,0.0) / resolution.x;
		vec2 chromatic_y = vec2(0.0, chromatic/2.0) / resolution.y;
		float r = Tri(pos - chromatic_x).r;
		float g = Tri(pos + chromatic_y).g;
		float b = Tri(pos + chromatic_x).b;
		clr = vec3(r,g,b);
	}
	
	if(grille_amount > 0.0)clr *= grille(pix);
	clr *= 1.0 + scan_line_amount * 0.6 + line * 3.0 + grille_amount * 2.0;
	if(vignette_amount > 0.0)clr *= vignette(pos);
	
	COLOR.rgb = clr;
	COLOR.a = 1.0;
}
```

## CRT Shader with realistic blurring
https://godotshaders.com/shader/crt-shader-with-realistic-blurring/
![[シェーダー-1746096803775.png|640x357]]
```python
//SHADER ORIGINALY CREADED BY "TimothyLottes" FROM SHADERTOY
//PORTED AND MODIFYED TO GODOT BY AHOPNESS (@ahopness)
//LICENSE : CC0
//COMATIBLE WITH : GLES2, GLES3, WEBGL
//SHADERTOY LINK : https://www.shadertoy.com/view/MsjXzh

// PUBLIC DOMAIN CRT STYLED SCAN-LINE SHADER
//
//   by Timothy Lottes
//
// This is more along the style of a really good CGA arcade monitor.
// With RGB inputs instead of NTSC.
// The shadow mask example has the mask rotated 90 degrees for less chromatic aberration.
//
// Left it unoptimized to show the theory behind the algorithm.
//
// It is an example what I personally would want as a display option for pixel art games.
// Please take and use, change, or whatever.

shader_type canvas_item;

// Emulated input resolution.
uniform vec2 res;

// Mask type
//  0 = Very compressed TV style shadow mask
//  1 = Stretched VGA style shadow mask (same as prior shaders)
//  2 = VGA style shadow mask 
uniform int mask_type :hint_range(0, 2) = 0;

// Bloom Type
//  0 = Normalized exposure
//  1 = Aditive bloom
//  2 = No Bloom
uniform int bloom_type :hint_range(0, 2) = 0;

// Hardness of scanline.
//  -8.0 = soft
// -16.0 = medium
uniform float hardScan :hint_range(-12.0, -1.0) = -8.0;

// Hardness of pixels in scanline.
// -2.0 = soft
// -4.0 = hard
uniform float hardPix :hint_range(-4.0, 0.0) = -2.0;

// Hardness of short vertical bloom.
//  -1.0 = wide to the point of clipping (bad)
//  -1.5 = wide
//  -4.0 = not very wide at all
uniform float hardBloomScan :hint_range(-4.0, 0.0) = -2.0;

// Hardness of short horizontal bloom.
//  -0.5 = wide to the point of clipping (bad)
//  -1.0 = wide
//  -2.0 = not very wide at all
uniform float hardBloomPix :hint_range(-2.0, 0.0) = -1.5;

// Amount of small bloom effect.
//  1.0/1.0 = only bloom
//  1.0/16.0 = what I think is a good amount of small bloom
//  0.0     = no bloom
uniform float bloomAmount :hint_range(1.0, 16.0) = 16.0;

// Display warp.
// 0.0 = none
// 1.0/8.0 = extreme
uniform vec2 warp = vec2(64.0, 24.0); 

// Amount of shadow mask.
uniform float maskDark  :hint_range(0.0, 1.0) = 0.5;
uniform float maskLight :hint_range(1.0, 2.0) = 1.5;

//------------------------------------------------------------------------

// sRGB to Linear.
// Assuing using sRGB typed textures this should not be needed.
float ToLinear1(float c){ return(c <= 0.04045) ? c / 12.92 : pow((c + 0.055) / 1.055, 2.4); }
vec3 ToLinear(vec3 c){ return vec3(ToLinear1(c.r), ToLinear1(c.g), ToLinear1(c.b)); }

// Linear to sRGB.
// Assuing using sRGB typed textures this should not be needed.
float ToSrgb1(float c){ return(c < 0.0031308?c * 12.92 : 1.055 * pow(c, 0.41666) - 0.055); }
vec3 ToSrgb(vec3 c){ return vec3(ToSrgb1(c.r), ToSrgb1(c.g), ToSrgb1(c.b)); }

// Nearest emulated sample given floating point position and texel offset.
// Also zero's off screen.
vec3 Fetch(vec2 pos, vec2 off, sampler2D iChannel0){
	pos = floor(pos * res + off) / res;
	
	if(max(abs(pos.x - 0.5), abs(pos.y - 0.5)) > 0.5){
		return vec3(0.0, 0.0, 0.0);
	}
	
	return ToLinear(texture(iChannel0 , pos.xy , -16.0).rgb);
}

// Distance in emulated pixels to nearest texel.
vec2 Dist(vec2 pos){ 
	pos = pos * res; 
	return - ((pos - floor(pos)) - vec2(0.5));
}
    
// 1D Gaussian.
float Gaus(float pos, float scale){ return exp2(scale * pos * pos); }

// 3-tap Gaussian filter along horz line.
vec3 Horz3(vec2 pos, float off, sampler2D iChannel0){
	vec3 b = Fetch(pos, vec2(-1.0, off), iChannel0);
	vec3 c = Fetch(pos, vec2( 0.0, off), iChannel0);
	vec3 d = Fetch(pos, vec2( 1.0, off), iChannel0);
	float dst = Dist(pos).x;
	
	// Convert distance to weight.
	float scale = hardPix;
	float wb = Gaus(dst - 1.0, scale);
	float wc = Gaus(dst + 0.0, scale);
	float wd = Gaus(dst + 1.0, scale);
	
	// Return filtered sample.
	return (b * wb + c * wc + d * wd) / (wb + wc + wd);
}
// 5-tap Gaussian filter along horz line.
vec3 Horz5(vec2 pos, float off, sampler2D iChannel0){
	vec3 a = Fetch(pos, vec2(-2.0, off), iChannel0);
	vec3 b = Fetch(pos, vec2(-1.0, off), iChannel0);
	vec3 c = Fetch(pos, vec2( 0.0, off), iChannel0);
	vec3 d = Fetch(pos, vec2( 1.0, off), iChannel0);
	vec3 e = Fetch(pos, vec2( 2.0, off), iChannel0);
	float dst = Dist(pos).x;
	
	// Convert distance to weight.
	float scale = hardPix;
	float wa = Gaus(dst - 2.0, scale);
	float wb = Gaus(dst - 1.0, scale);
	float wc = Gaus(dst + 0.0, scale);
	float wd = Gaus(dst + 1.0, scale);
	float we = Gaus(dst + 2.0, scale);
	
	// Return filtered sample.
	return (a * wa + b * wb + c * wc + d * wd + e * we) / (wa + wb + wc + wd + we);
}
// 7-tap Gaussian filter along horz line.
vec3 Horz7(vec2 pos, float off, sampler2D iChannel0){
	vec3 a = Fetch(pos, vec2(-3.0, off), iChannel0);
	vec3 b = Fetch(pos, vec2(-2.0, off), iChannel0);
	vec3 c = Fetch(pos, vec2( 1.0, off), iChannel0);
	vec3 d = Fetch(pos, vec2( 0.0, off), iChannel0);
	vec3 e = Fetch(pos, vec2( 1.0, off), iChannel0);
	vec3 f = Fetch(pos, vec2( 2.0, off), iChannel0);
	vec3 g = Fetch(pos, vec2( 3.0, off), iChannel0);
	float dst = Dist(pos).x;
	
	// Convert distance to weight.
	float scale = hardBloomPix;
	float wa = Gaus(dst - 3.0, scale);
	float wb = Gaus(dst - 2.0, scale);
	float wc = Gaus(dst - 1.0, scale);
	float wd = Gaus(dst + 0.0, scale);
	float we = Gaus(dst + 1.0, scale);
	float wf = Gaus(dst + 2.0, scale);
	float wg = Gaus(dst + 3.0, scale);
	
	// Return filtered sample.
	return (a * wa + b * wb + c * wc + d * wd + e * we + f * wf + g * wg) / (wa + wb + wc + wd + we + wf + wg);
}

// Return scanline weight.
float Scan(vec2 pos, float off){
	float dst = Dist(pos).y;
	
	return Gaus(dst + off, hardScan);
}

// Return scanline weight for bloom.
float BloomScan(vec2 pos, float off){
	float dst = Dist(pos).y;
	
	return Gaus(dst + off, hardBloomScan);
}

// Allow nearest three lines to effect pixel.
vec3 Tri(vec2 pos, sampler2D iChannel0){
	vec3 a = Horz3(pos,-1.0, iChannel0);
	vec3 b = Horz5(pos, 0.0, iChannel0);
	vec3 c = Horz3(pos, 1.0, iChannel0);
	
	float wa = Scan(pos,-1.0);
	float wb = Scan(pos, 0.0);
	float wc = Scan(pos, 1.0);
	
	return a * wa + b * wb + c * wc;
}

// Small bloom.
vec3 Bloom(vec2 pos, sampler2D iChannel0){
	vec3 a = Horz5(pos,-2.0, iChannel0);
	vec3 b = Horz7(pos,-1.0, iChannel0);
	vec3 c = Horz7(pos, 0.0, iChannel0);
	vec3 d = Horz7(pos, 1.0, iChannel0);
	vec3 e = Horz5(pos, 2.0, iChannel0);
	
	float wa = BloomScan(pos,-2.0);
	float wb = BloomScan(pos,-1.0);
	float wc = BloomScan(pos, 0.0);
	float wd = BloomScan(pos, 1.0);
	float we = BloomScan(pos, 2.0);
	
	return a * wa + b * wb + c * wc + d * wd + e * we;
}

// Distortion of scanlines, and end of screen alpha.
vec2 Warp(vec2 pos){
	pos = pos * 2.0 - 1.0;    
	pos *= vec2(1.0 + (pos.y * pos.y) * 1.0 / warp.x, 1.0 + (pos.x * pos.x) * 1.0/ warp.y);
	
	return pos * 0.5+0.5;
}

vec3 Mask(vec2 pos){
	if (mask_type == 0){
		float line = maskLight;
		float odd = 0.0;
		
		if(fract(pos.x / 6.0) < 0.5) odd = 1.0;
		if(fract((pos.y + odd) / 2.0) < 0.5) line = maskDark;  
		pos.x = fract(pos.x / 3.0);
		
		vec3 mask = vec3(maskDark, maskDark, maskDark);
		if(pos.x < 0.333)mask.r = maskLight;
		else if(pos.x < 0.666)mask.g = maskLight;
		else mask.b = maskLight;
		
		mask *= line;
		return mask;
	}else if (mask_type == 1){
		pos.x += pos.y * 3.0;
		
		vec3 mask = vec3(maskDark, maskDark, maskDark);
		pos.x = fract(pos.x / 6.0);
		if(pos.x < 0.333)mask.r = maskLight;
		else if(pos.x < 0.666)mask.g = maskLight;
		else mask.b = maskLight;
		
		return mask;
	}else if (mask_type == 2){
		pos.xy = floor(pos.xy * vec2(1.0, 0.5));
		pos.x += pos.y * 3.0;
		
		vec3 mask = vec3(maskDark, maskDark, maskDark);
		pos.x = fract(pos.x / 6.0);
		if(pos.x < 0.333)mask.r = maskLight;
		else if(pos.x < 0.666)mask.g = maskLight;
		else mask.b = maskLight;
	
		return mask;
	}
  }    

// Draw dividing bars.
float Bar(float pos, float bar){ pos -= bar; return pos * pos < 4.0 ? 0.0 : 1.0; }

// Entry.
void fragment(){
	vec2 pos = Warp(FRAGCOORD.xy / (1.0 / SCREEN_PIXEL_SIZE).xy);
	
	COLOR.rgb = Tri(pos, SCREEN_TEXTURE) * Mask(FRAGCOORD.xy);
	if (bloom_type == 0){ 
		COLOR.rgb = mix(COLOR.rgb,Bloom(pos, SCREEN_TEXTURE), 1.0 / bloomAmount);    
	}else if (bloom_type == 1){
		COLOR.rgb += Bloom(pos, SCREEN_TEXTURE) * 1.0 / bloomAmount;    
	} 
	
	COLOR.a = 1.0;  
	COLOR.rgb = ToSrgb(COLOR.rgb);
}
```

## Glitch Effect Shader for Godot Engine 4
https://godotshaders.com/shader/glitch-effect-shader-for-godot-engine-4/
![[シェーダー-1746096349074.png|640x357]]
```python
/*
	Glitch Effect Shader by Yui Kinomoto @arlez80
	MIT License
*/

shader_type canvas_item;

// 振動の強さ
uniform float shake_power = 0.03;
// 振動率
uniform float shake_rate : hint_range( 0.0, 1.0 ) = 0.2;
// 振動速度
uniform float shake_speed = 5.0;
// 振動ブロックサイズ
uniform float shake_block_size = 30.5;
// 色の分離率
uniform float shake_color_rate : hint_range( 0.0, 1.0 ) = 0.01;
// スクリーン
uniform sampler2D screen_texture : hint_screen_texture;

float random( float seed )
{
	return fract( 543.2543 * sin( dot( vec2( seed, seed ), vec2( 3525.46, -54.3415 ) ) ) );
}

void fragment( )
{
	float enable_shift = float(
		random( trunc( TIME * shake_speed ) )
	<	shake_rate
	);

	vec2 fixed_uv = SCREEN_UV;
	fixed_uv.x += (
		random(
			( trunc( SCREEN_UV.y * shake_block_size ) / shake_block_size )
		+	TIME
		) - 0.5
	) * shake_power * enable_shift;

	vec4 pixel_color = textureLod( screen_texture, fixed_uv, 0.0 );
	pixel_color.r = mix(
		pixel_color.r
	,	textureLod( screen_texture, fixed_uv + vec2( shake_color_rate, 0.0 ), 0.0 ).r
	,	enable_shift
	);
	pixel_color.b = mix(
		pixel_color.b
	,	textureLod( screen_texture, fixed_uv + vec2( -shake_color_rate, 0.0 ), 0.0 ).b
	,	enable_shift
	);
	COLOR = pixel_color;
}
```


## VHS Glitch
https://godotshaders.com/shader/vhs-glitch/
![[シェーダー-1746096411419.png|640x357]]
```python
shader_type canvas_item;

uniform float range : hint_range(0.0, 0.1, 0.005)= 0.05;
uniform float noiseQuality : hint_range(0.0, 300.0, 0.1)= 250.0;
uniform float noiseIntensity : hint_range(-0.6, 0.6, 0.0010)= 0.0088;
uniform float offsetIntensity : hint_range(-0.1, 0.1, 0.001) = 0.03;
uniform float colorOffsetIntensity : hint_range(0.0, 5.0, 0.001) = 1.3;
uniform sampler2D SCREEN_TEXTURE : hint_screen_texture;
float rand(vec2 co)
{
    return fract(sin(dot(co.xy ,vec2(12.9898,78.233))) * 43758.5453);
}

float verticalBar(float pos, float UVY, float offset)
{
    float edge0 = (pos - range);
    float edge1 = (pos + range);

    float x = smoothstep(edge0, pos, UVY) * offset;
    x -= smoothstep(pos, edge1, UVY) * offset;
    return x;
}
const float saturation = 0.2;
void fragment()
{
    vec2 uv = SCREEN_UV;
    for (float i = 0.0; i < 0.71; i += 0.1313)
    {
        float d = mod(TIME * i, 1.7);
        float o = sin(1.0 - tan(TIME * 0.24 * i));
    	o *= offsetIntensity;
        uv.x += verticalBar(d, UV.y, o);
    }
    
    float UVY = uv.y;
    UVY *= noiseQuality;
    UVY = float(int(UVY)) * (1.0 / noiseQuality);
    float noise = rand(vec2(TIME * 0.00001, UVY));
    uv.x += noise * noiseIntensity;

    vec2 offsetR = vec2(0.009 * sin(TIME), 0.0) * colorOffsetIntensity;
    vec2 offsetG = vec2(0.0073 * (cos(TIME * 0.97)), 0.0) * colorOffsetIntensity;
    
    float r = texture(SCREEN_TEXTURE, uv + offsetR).r;
    float g = texture(SCREEN_TEXTURE, uv + offsetG).g;
    float b = texture(SCREEN_TEXTURE, uv).b;
    vec4 tex = vec4(r, g, b, 1.0);
    COLOR = tex;
}
```

## Pixelated Glitch PostEffect
https://godotshaders.com/shader/pixelated-glitch-posteffect/
![[シェーダー-1746096469658.png|640x357]]
```python
shader_type canvas_item;

uniform float pixel = 1.0;
uniform vec2 red_offset = vec2(0.0, 0.0);
uniform vec2 green_offset = vec2(0.0, 0.0);
uniform vec2 blue_offset = vec2(0.0, 0.0);
uniform float alpha = 1.0;
uniform float rand_strength = 1.0;

uniform vec4 affected = vec4(0.0, 0.0, 1.0, 1.0);

float Hash21(vec2 p) {
	p = fract(p*vec2(123.34, 456.21));
	p += dot(p, p+45.32);
	
	return fract(p.x*p.y);
}

vec2 pixelize(vec2 uv) {
	float mult = 5000.0 / (pixel * pixel);
	uv.x = floor(uv.x * mult) / mult;
	uv.y = floor(uv.y * mult) / mult;
	return uv;
}

vec4 chroma(vec2 uv, sampler2D tex) {
	vec4 col = texture(tex, uv);
	if (abs(red_offset.x) + abs(red_offset.y) > 0.001) {
		col.r = texture(tex, vec2(uv.x + red_offset.x, uv.y + red_offset.y)).r;
	}
	if (abs(green_offset.x) + abs(green_offset.y) > 0.001) {
		col.g = texture(tex, vec2(uv.x + green_offset.x, uv.y + green_offset.y)).g;	
	}
	if (abs(blue_offset.x) + abs(blue_offset.y) > 0.001) {
		col.b = texture(tex, vec2(uv.x + blue_offset.x, uv.y + blue_offset.y)).b;
	}
	
	return col;
}

void fragment() {
	vec2 normal_uv = UV;
	vec4 normal_col = texture(TEXTURE, UV);
    if (normal_uv.x < affected.x || normal_uv.y < affected.y || normal_uv.x > affected.x + affected.z || normal_uv.y > affected.y + affected.a) {
		COLOR = normal_col;
	}
	else {
		vec2 uv = pixelize(UV);
		vec4 col = chroma(uv, TEXTURE);
		col.a = col.a * alpha;
		vec2 id = floor(uv * 10.0);
		float alpha_rand = Hash21(id + floor(TIME*10.0));
		col = col * ( alpha_rand + (rand_strength * (1.0-alpha_rand)) );
		COLOR = col;
	}
}
```

## 3D Glitch
https://godotshaders.com/shader/3d-glitch/
![[シェーダー-1746096527504.png|640x357]]
```python
shader_type spatial;

// Glitch intensity
uniform float shake_power = 0.5;

// Probability
uniform float shake_rate : hint_range(0.0, 1.0) = 0.5;

uniform float shake_speed = 5.0;

// Hard to describe, change it and monitor result
uniform float shake_block_size = 30.5;

uniform float shake_color_rate : hint_range(0.0, 1.0) = 0.5;

uniform sampler2D main_tex : filter_nearest, source_color;

varying float enable_shift;

float random(float seed) {
    return fract(sin(seed * 12345.678) * 43758.5453);
}

void vertex() {
	float adjusted_time = mod(TIME, 5.0);

    enable_shift = float(random(trunc(adjusted_time * shake_speed)) < shake_rate);

    float offset_x = (random((trunc(VERTEX.y * shake_block_size) / shake_block_size) + adjusted_time) - 0.5) * shake_power * enable_shift;
    VERTEX.x += offset_x;
}

void fragment() {
	float adjusted_time = mod(TIME, 5.0);
    vec2 fixed_uv = UV;
    fixed_uv.x += (
        random((trunc(UV.y * shake_block_size) / shake_block_size) + adjusted_time) - 0.5
    ) * shake_power * enable_shift;

    vec4 pixel_color = texture(main_tex, fixed_uv);

    pixel_color.r = mix(
        pixel_color.r,
        texture(main_tex, fixed_uv + vec2(shake_color_rate, 0.0)).r,
        enable_shift
    );
    pixel_color.b = mix(
        pixel_color.b,
        texture(main_tex, fixed_uv + vec2(-shake_color_rate, 0.0)).b,
        enable_shift
    );

    ALBEDO = pixel_color.rgb;
}
```

## Gaussian Blur Functions for GLES2
https://godotshaders.com/shader/gaussian-blur-functions-for-gles2/
![[シェーダー-1746096936412.png|640x357]]
```python
shader_type canvas_item;

// Xor's gausian blur function 
// Link: https://xorshaders.weebly.com/tutorials/blur-shaders-5-part-2
// Defaults from: https://www.shadertoy.com/view/Xltfzj
//
// BLUR BLURRINESS (Default 8.0)
// BLUR ITERATIONS (Default 16.0 - More is better but slower)
// BLUR QUALITY (Default 4.0 - More is better but slower)
//
// Desc.: Don't have the best performance but will run on almost
// anything, although, if developing for mobile, is better to use 
// 'texture_nodevgaussian(...) instead'.
vec4 texture_xorgaussian(sampler2D tex, vec2 uv, vec2 pixel_size, float blurriness, int iterations, int quality){
	float pi = 6.28;
	
	vec2 radius = blurriness / (1.0 / pixel_size).xy;
	vec4 blurred_tex = texture(tex, uv);
	
	for(float d = 0.0; d < pi; d += pi / float(iterations)){
		for( float i = 1.0 / float(quality); i <= 1.0; i += 1.0 / float(quality) ){
			vec2 directions = uv + vec2(cos(d), sin(d)) * radius * i;
			blurred_tex += texture(tex, directions);
		}
	}
	blurred_tex /= float(quality) * float(iterations) + 1.0;
	
	return blurred_tex;
}

// Experience-Monks' fast gaussian blur function
// Link: https://github.com/Experience-Monks/glsl-fast-gaussian-blur/
//
// BLUR ITERATIONS (Default 16.0 - More is better but slower)
// BLUR DIRECTION (Direction in which the blur is apllied, use vec2(1, 0) for first pass and vec2(0, 1) for second pass)
//
// Desc.: ACTUALLY PRETTY SLOW but still pretty good for custom cinematic
// bloom effects, since this needs render 2 passes 
vec4 texture_monksgaussian_multipass(sampler2D tex, vec2 uv, vec2 pixel_size, int iterations, vec2 direction){
	vec4 blurred_tex = vec4(0.0);
	vec2 resolution = 1.0 / pixel_size;
	
	for (int i=0; i < iterations; i++){
		float size = float(iterations - i);
		
		vec2 off1 = vec2(1.3846153846) * (direction * size);
		vec2 off2 = vec2(3.2307692308) * (direction * size);

		blurred_tex += texture(tex, uv) * 0.2270270270;
		blurred_tex += texture(tex, uv + (off1 / resolution)) * 0.3162162162;
		blurred_tex += texture(tex, uv - (off1 / resolution)) * 0.3162162162;
		blurred_tex += texture(tex, uv + (off2 / resolution)) * 0.0702702703;
		blurred_tex += texture(tex, uv - (off2 / resolution)) * 0.0702702703;
	}
	
	blurred_tex /= float(iterations) + 1.0;
	
	return blurred_tex;
}

// u/_NoDev_'s gaussian blur function
// Discussion Link: https://www.reddit.com/r/godot/comments/klgfo9/help_with_shaders_in_gles2/
// Code Link: https://postimg.cc/7JDJw80d
//
// BLUR BLURRINESS (Default 8.0 - More is better but slower)
// BLUR RADIUS (Default 1.5)
// BLUR DIRECTION (Direction in which the blur is apllied, use vec2(1, 0) for first pass and vec2(0, 1) for second pass)
//
// Desc.: Really fast and GOOD FOR MOST CASES, but might NOT RUN IN THE WEB!
// use 'texture_xorgaussian' instead if you found any issues.
vec4 texture_nodevgaussian_singlepass(sampler2D tex, vec2 uv, vec2 pixel_size, float blurriness, float radius){
	float pi = 3.14;
	float n = 0.0015;
	
	vec4 blurred_tex = vec4(0);
	
	float weight;
	for (float i = -blurriness; i <= blurriness; i++){
		float d = i / pi;
		vec2 anchor = vec2(cos(d), sin(d)) * radius * i;
		vec2 directions = uv + pixel_size * anchor;
		blurred_tex += texture(tex, directions) * n;
		if (i <= 0.0) {n += 0.0015; }
		if (i > 0.0) {n -= 0.0015; }
		weight += n;
	}
	
	float norm = 1.0 / weight;
	blurred_tex *= norm;
	return blurred_tex;
}
vec4 texture_nodevgaussian_multipass(sampler2D tex, vec2 uv, vec2 pixel_size, float blurriness, vec2 direction){
	float n = 0.0015;
	
	vec4 blurred_tex = vec4(0);
	
	float weight;
	for (float i = -blurriness; i <= blurriness; i++){
		vec2 directions = uv + pixel_size * (direction * i);
		blurred_tex += texture(tex, directions) * n;
		if (i <= 0.0) {n += 0.0015; }
		if (i > 0.0) {n -= 0.0015; }
		weight += n;
	}
	
	float norm = 1.0 / weight;
	blurred_tex *= norm;
	return blurred_tex;
}

//  -- EXAMPLE CODE -- //
uniform int blur_type = 0;
uniform int blur_amount = 16;
uniform float blur_radius = 1;
uniform vec2 blur_direction = vec2(1, 1);
void fragment(){
	vec2 uv = FRAGCOORD.xy / (1.0 / SCREEN_PIXEL_SIZE).xy;
	
	if (blur_type == 0) 
	{
		vec4 xorgaussian = texture_xorgaussian(SCREEN_TEXTURE, uv, SCREEN_PIXEL_SIZE, float(blur_amount), 16, 4);
		COLOR =  xorgaussian;
	} 
	else if (blur_type == 1) 
	{
		vec4 monksgaussian_multipass = texture_monksgaussian_multipass(SCREEN_TEXTURE, uv, SCREEN_PIXEL_SIZE, blur_amount, blur_direction);
		COLOR =  monksgaussian_multipass;
	} 
	else if (blur_type == 2) 
	{
		vec4 nodevgaussian_singlepass = texture_nodevgaussian_singlepass(SCREEN_TEXTURE, uv, SCREEN_PIXEL_SIZE, float(blur_amount), blur_radius);
		COLOR =  nodevgaussian_singlepass;
	} 
	else if (blur_type == 3) 
	{
		vec4 nodevgaussian_multipass = texture_nodevgaussian_multipass(SCREEN_TEXTURE, uv, SCREEN_PIXEL_SIZE, float(blur_amount), blur_direction);
		COLOR =  nodevgaussian_multipass;
	} 
	else 
	{
		COLOR =  texture(SCREEN_TEXTURE, uv);
	}
}
```

## Cosine Water
https://godotshaders.com/shader/cosine-water-2/
![[シェーダー-1746097053854.png|640x357]]
```python
shader_type canvas_item;

#define iResolution 1.0/SCREEN_PIXEL_SIZE
#define iTime TIME
#define fragColor COLOR

uniform float uv_scale : hint_range(0.0, 10.0, 0.1) = 1.0;
uniform float color_alpha : hint_range(0.0, 1.0, 0.1) = 1.0;

vec2 hash( vec2 p ) // replace this by something better
{
    p = vec2( dot(p,vec2(127.1,311.7)), dot(p,vec2(269.5,183.3)) );
    return -1.0 + 2.0*fract(sin(p)*43758.5453123);
}

float noise( in vec2 p )
{
    const float K1 = 0.366025404; // (sqrt(3)-1)/2;
    const float K2 = 0.211324865; // (3-sqrt(3))/6;

    vec2  i = floor( p + (p.x+p.y)*K1 );
    vec2  a = p - i + (i.x+i.y)*K2;
    float m = step(a.y,a.x); 
    vec2  o = vec2(m,1.0-m);
    vec2  b = a - o + K2;
    vec2  c = a - 1.0 + 2.0*K2;
    vec3  h = max( 0.5-vec3(dot(a,a), dot(b,b), dot(c,c) ), 0.0 );
    vec3  n = h*h*h*h*vec3( dot(a,hash(i+0.0)), dot(b,hash(i+o)), dot(c,hash(i+1.0)));
    return dot( n, vec3(70.0) );
}

#define MAX_WAVES 4
#define SUPERPOSITION 4
#define TAU 6.28318
#define PHI 1.618

// closed form normal would increase performance a lot
float height(vec2 p, float t) {
    float acc = 0.0;
    for (int i = 0; i < MAX_WAVES; i++) {
    for (int j = 0; j < SUPERPOSITION; j++) {
        int seed = i + 5*j;
        //float theta = TAU * noise(vec2(0.01 * t, 10.0*float(i)));
        float theta = TAU * PHI * 10.0*float(seed);
        float up = cos(theta) * p.x - sin(theta) * p.y;
        float vp = sin(theta) * p.x + cos(theta) * p.y;
        //float initial_phase = TAU * noise(vec2(0.0, 2.0*float(i))) + cos(vp);
        float initial_phase = TAU * PHI * float(seed);
        //float k = pow(2.0, float(i)*0.1) + 0.5;
        //float k = pow(2.0, 1.0 + 0.4*float(i));
        float k = pow(2.0, float(i));
        //float k = float(i+1);
        float phase = initial_phase + up*k + cos(vp) + 3.0*t + 0.5*k*t;
        // its kinda like choose your artifacts, probably use noise for vp
        float A = cos(phase)/(k*k);
        acc += A;
    }}
    return acc;
}
vec4 hn_fdm(vec2 p, float t) {
  float h = height(p, t);
  vec2 vx = vec2(0.1, 0.0);
  vec2 vy = vec2(0.0, 0.1);
  float hx = height(p+vx, t);
  float hy = height(p+vy, t);
  float dx = (hx - h);
  float dy = (hy - h);
  // vec3 norm = normalize(vec3(-dx, -dy, dx+dy));
  // vec3 norm = normalize(vec3(-dx/vx.x, -dy/vy.y, 1.0));

  vec3 v1 = normalize(vec3(vx.x, 0.0, dx));
  vec3 v2 = normalize(vec3(0.0, vy.y, dy));
  vec3 norm = cross(v1, v2);

  return vec4(norm, h);
}

void fragment()
{
    vec2 uv = UV;
    vec2 uv_screen = (uv - 0.5) * uv_scale;

    vec4 nh = hn_fdm(uv_screen* 10.0, iTime * 1.0);
    float h = nh.w;
    vec3 norm = nh.xyz;
    vec3 sun_dir = normalize(vec3(-0.2, 0.4, 1.0));

    vec4 water_colour = vec4(0.2, 0.4, 0.6, 1.0);
    vec4 foam_colour = vec4(0.8, 0.9, 1.0, 1.0);
    vec4 sky_colour = vec4(0.2, 0.6, 0.8, 1.0);
    vec4 specular_colour = vec4(1.0, 1.0, 1.0, 1.0);

    //fragColor = vec4(norm.xyz, 1.0); return;
    
    if (dot(sun_dir, norm) > 0.98) {
        fragColor = specular_colour;
    } else {
        fragColor = mix(water_colour, sky_colour, dot(norm, normalize(vec3(0.0, 0.2, 1.0))));
    }
	fragColor.a = color_alpha;
}
```

## water with primitive caustics
https://godotshaders.com/shader/water-with-primitive-caustics/
![[シェーダー-1746097123695.png|640x357]]
```python
shader_type canvas_item;

#define iTime TIME
#define fragColor COLOR

uniform sampler2D iChannel0;
uniform sampler2D iChannel1;
uniform vec2 param1 = vec2(0.3, 0.2);
uniform vec2 param2 = vec2(0.4, 0.7);
uniform vec2 param3 = vec2(0.3, 0.1);

void fragment()
{
    vec2 uv = UV;
    vec2 dist = vec2(0.0);
    dist += texture(iChannel1,fract(uv+vec2(iTime*0.2,0.0))).rr * param1;
    dist += texture(iChannel1,fract(uv+vec2(iTime*-0.1,iTime*-0.1))).rr * param2;
    dist += texture(iChannel1,fract(uv+vec2(iTime*-0.03,iTime*0.2))).rr * param3;
    vec3 col = texture(iChannel0,uv - dist * 0.02 * 1.).rgb;
    col += pow(dot(normalize(vec3(dist.x,1.0,dist.y)),vec3(0.0,1.0,0.0)),16.0);
    fragColor = vec4(col,1.0);
}
```

## notebook drawings
https://godotshaders.com/shader/notebook-drawings/
![[シェーダー-1746097182085.png|640x357]]
```python
shader_type canvas_item;

#define iTime TIME

// created by florian berger (flockaroo) - 2016
// License Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License.

// trying to resemle some hand drawing style



#define Res0 vec2(float(textureSize(iChannel0,0).x), float(textureSize(iChannel0,0).y))
#define Res1 vec2(float(textureSize(iChannel1,0).x), float(textureSize(iChannel1,0).y))
#define iResolution Res0


#define Res  iResolution.xy

uniform vec2 disp = vec2(200.0, 100.);
uniform float zoom : hint_range(0.05, 1.0, 0.05) = 0.5;
uniform sampler2D iChannel0;
uniform sampler2D iChannel1;


vec4 getRand(vec2 pos)
{
    return textureLod(iChannel1,pos/Res1/iResolution.y*1080., 0.0);
}

vec4 getCol(vec2 pos)
{
    // take aspect ratio into account
    vec2 uv=((pos-Res.xy*.5)/Res.y*Res0.y)/Res0.xy+.5;
    vec4 c1=texture(iChannel0,uv);
    vec4 e=smoothstep(vec4(-0.05),vec4(-0.0),vec4(uv,vec2(1)-uv));
    c1=mix(vec4(1,1,1,0),c1,e.x*e.y*e.z*e.w);
    float d=clamp(dot(c1.xyz,vec3(-.5,1.,-.5)),0.0,1.0);
    vec4 c2=vec4(.7);
    return min(mix(c1,c2,1.8*d),.7);
}

vec4 getColHT(vec2 pos)
{
 	return smoothstep(.95,1.05,getCol(pos)*.8+.2+getRand(pos*.7));
}

float getVal(vec2 pos)
{
    vec4 c=getCol(pos);
 	return pow(dot(c.xyz,vec3(.333)),1.)*1.;
}

vec2 getGrad(vec2 pos, float eps)
{
   	vec2 d=vec2(eps,0);
    return vec2(
        getVal(pos+d.xy)-getVal(pos-d.xy),
        getVal(pos+d.yx)-getVal(pos-d.yx)
    )/eps/2.;
}

#define AngleNum 3

#define SampNum 16
#define PI2 6.28318530717959

void fragment()
{
    vec2 pos = (FRAGCOORD.xy+4.0*sin(iTime*1.*vec2(1,1.7))*iResolution.y/400. - disp) * zoom;
    vec3 col = vec3(0);
    vec3 col2 = vec3(0);
    float sum=0.;
    for(int i=0;i<AngleNum;i++)
    {
        float ang=PI2/float(AngleNum)*(float(i)+.8);
        vec2 v=vec2(cos(ang),sin(ang));
        for(int j=0;j<SampNum;j++)
        {
            vec2 dpos  = v.yx*vec2(1,-1)*float(j)*iResolution.y/400.;
            vec2 dpos2 = v.xy*float(j*j)/float(SampNum)*.5*iResolution.y/400.;
	        vec2 g;
            float fact;
            float fact2;

            for(float s=-1.;s<=1.;s+=2.)
            {
                vec2 pos2=pos+s*dpos+dpos2;
                vec2 pos3=pos+(s*dpos+dpos2).yx*vec2(1,-1)*2.;
            	g=getGrad(pos2,.4);
            	fact=dot(g,v)-.5*abs(dot(g,v.yx*vec2(1,-1)))/**(1.-getVal(pos2))*/;
            	fact2=dot(normalize(g+vec2(.0001)),v.yx*vec2(1,-1));
                
                fact=clamp(fact,0.,.05);
                fact2=abs(fact2);
                
                fact*=1.-float(j)/float(SampNum);
            	col += fact;
            	col2 += fact2*getColHT(pos3).xyz;
            	sum+=fact2;
            }
        }
    }
    col/=float(SampNum*AngleNum)*.75/sqrt(iResolution.y);
    col2/=sum;
    col.x*=(.6+.8*getRand(pos*.7).x);
    col.x=1.-col.x;
    col.x*=col.x*col.x;

    vec2 s=sin(pos.xy*.1/sqrt(iResolution.y/400.));
    vec3 karo=vec3(1);
    karo-=.5*vec3(.25,.1,.1)*dot(exp(-s*s*80.),vec2(1));
    float r=length(pos-iResolution.xy*.5)/iResolution.x;
    float vign=1.-r*r*r;
	COLOR = vec4(vec3(col.x*col2*karo*vign),1);
    //COLOR=getCol(FRAGCOORD.xy * 0.5);
}
```

## Select Color Range
https://godotshaders.com/shader/select-color-range/
![[シェーダー-1746097255933.png|640x357]]
```python
shader_type canvas_item;

#define iResolution 1.0 / SCREEN_PIXEL_SIZE
#define iTime TIME
#define fragColor COLOR
#define fragCoord FRAGCOORD

uniform sampler2D iChannel0;
uniform vec4 iMouse = vec4(0.);

// The MIT License
// Copyright © 2023 Gehtsiegarnixan
// Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions: The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software. THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
/*
This is a small trial to generate a feature similar to the Color Range Selection in 
Photoshop. The image shows the selected color from the image in the cursor, and then 
blends between this color and its inverse using the generated grayscale mask.

This shader has mouse controls that allow you to change the selected color using the 
mouse. Additionally, you can change the range and fuzziness of the selection in the 
global constants below, or set the target color directly.
*/

// how much deviation from the color is allowed that is still 100%
#define RANGE 0.1

// Softness of the fadout for the selected color
#define FUZZINESS 0.3

// Uncomment to use a specific color to isolate that may not be in the image
//#define REFERENCE_COLOR vec3(0.961,0.980,0.957)

// Generates a smooth mask of isolating a target color
float colorRange(vec3 pixelColor, vec3 targetColor, float range, float fuzziness) {
    // Calculate the Euclidean distance between pixelColor and targetColor
    float distance = length(pixelColor - targetColor);

    // Normalize the distance by the maximum possible distance
    float normalizedDistance = distance / sqrt(3.0);

    // Colors within Range become 1 and colors within Range+ Fuzziness fade to 0
    return smoothstep(range + fuzziness, range, normalizedDistance);
}

void fragment()
{
    // Normalized pixel coordinates (from 0 to 1)
    vec2 uv = UV;

    // Sample color Texture
    vec3 imageColor = texture(iChannel0, uv).xyz;
    
    #ifdef REFERENCE_COLOR
        // overwrite the reference color with a global constant
        vec3 referenceColor = REFERENCE_COLOR;
        
    #else
        // initilaize some variables 
        vec2 sampleCoords;
        vec2 cursor;
        
        // If the mouse was clicked, scale mouse coordinates to the 0-1 range of the screen
        if (iMouse.x > 0.0) {

            // set the sample position at mouse posiiton
            sampleCoords = iMouse.xy / iResolution.xy;
           
        } else {
            // If the mouse was not clicked, animate the direction vector
            float myTime = iTime * 0.05;
            sampleCoords = vec2(cos(myTime), sin(myTime))*0.2+0.5;        
        }
        
        vec2 coords = fragCoord.xy / iResolution.xy;
        
        // Generate Mouse coursor, x = mouse color, y = mouse weight 
        float mouseSize = 0.02;
        float mouseDist = length((coords - sampleCoords) * vec2(iResolution.x/iResolution.y, 1.));
        
        float aa = fwidth(mouseDist); // cheap anti-aliasing factor
        
        // Mask for the outline and the white center of the sphere cursor
        cursor.x = smoothstep(mouseSize*0.5 + aa, mouseSize*0.5 - aa, mouseDist);
        cursor.y = smoothstep(mouseSize + aa, mouseSize - aa, mouseDist);

        // sample the coordinates of the texture to find the reference color
        vec3 referenceColor = texture(iChannel0, sampleCoords).xyz;       
    #endif
    
    // Apply color range selection
    float mask = colorRange(imageColor, referenceColor, RANGE, FUZZINESS);
    
    // Use mask to make make of color and it's inverse
    vec3 color = mix(1.-referenceColor, referenceColor, mask);

    #ifndef REFERENCE_COLOR
        // Add mouse cursor
        color = mix(color, cursor.x * referenceColor, cursor.y);
    #endif
    
    // Output to screen
    fragColor = vec4(color,1);
}
```

## 20151110_VHS
https://godotshaders.com/shader/20151110_vhs/
![[シェーダー-1746097698775.png|640x360]]
```python
shader_type canvas_item;

#define time iTime
#define resolution ( iResolution.xy )
#define gl_FragCoord fragCoord
#define gl_FragColor fragColor
#define fragCoord FRAGCOORD
#define fragColor COLOR
#define iResolution 1.0/SCREEN_PIXEL_SIZE
#define iTime TIME

uniform float speed : hint_range(-20.0, 20.0, 0.1) = 8.0;
uniform sampler2D iChannel0;

vec3 tex2D( sampler2D _tex, vec2 _p ){
  vec3 col = texture( _tex, _p ).xyz;
  if ( 0.5 < abs( _p.x - 0.5 ) ) {
    col = vec3( 0.1 );
  }
  return col;
}

float hash( vec2 _v ){
  return fract( sin( dot( _v, vec2( 89.44, 19.36 ) ) ) * 22189.22 );
}

float iHash( vec2 _v, vec2 _r ){
  float h00 = hash( vec2( floor( _v * _r + vec2( 0.0, 0.0 ) ) / _r ) );
  float h10 = hash( vec2( floor( _v * _r + vec2( 1.0, 0.0 ) ) / _r ) );
  float h01 = hash( vec2( floor( _v * _r + vec2( 0.0, 1.0 ) ) / _r ) );
  float h11 = hash( vec2( floor( _v * _r + vec2( 1.0, 1.0 ) ) / _r ) );
  vec2 ip = vec2( smoothstep( vec2( 0.0, 0.0 ), vec2( 1.0, 1.0 ), mod( _v*_r, 1. ) ) );
  return ( h00 * ( 1. - ip.x ) + h10 * ip.x ) * ( 1. - ip.y ) + ( h01 * ( 1. - ip.x ) + h11 * ip.x ) * ip.y;
}

float noise( vec2 _v ){
  float sum = 0.;
  for( int i=1; i<9; i++ )
  {
    sum += iHash( _v + vec2( float(i) ), vec2( 2. * pow( 2., float( i ) ) ) ) / pow( 2., float( i ) );
  }
  return sum;
}

void fragment(){
  vec2 uv = gl_FragCoord.xy / resolution;
  vec2 uvn = uv;
  vec3 col = vec3( 0.0 );

  // tape wave
  uvn.x += ( noise( vec2( uvn.y, time ) ) - 0.5 )* 0.005;
  uvn.x += ( noise( vec2( uvn.y * 100.0, time * 10.0 ) ) - 0.5 ) * 0.01;

  // tape crease
  float tcPhase = clamp( ( sin( uvn.y * speed - time * PI * 1.2 ) - 0.92 ) * noise( vec2( time ) ), 0.0, 0.01 ) * 10.0;
  float tcNoise = max( noise( vec2( uvn.y * 100.0, time * 10.0 ) ) - 0.5, 0.0 );
  uvn.x = uvn.x - tcNoise * tcPhase;

  // switching noise
  float snPhase = smoothstep( 0.03, 0.0, uvn.y );
  uvn.y += snPhase * 0.3;
  uvn.x += snPhase * ( ( noise( vec2( uv.y * 100.0, time * 10.0 ) ) - 0.5 ) * 0.2 );
    
  col = tex2D( iChannel0, vec2(uvn.x, uvn.y) );
  col *= 1.0 - tcPhase;
  col = mix(
    col,
    col.yzx,
    snPhase
  );

  // bloom
  for( float x = -4.0; x < 2.5; x += 1.0 ){
    col.xyz += vec3(
      tex2D( iChannel0, uvn + vec2( x - 0.0, 0.0 ) * 7E-3 ).x,
      tex2D( iChannel0, uvn + vec2( x - 2.0, 0.0 ) * 7E-3 ).y,
      tex2D( iChannel0, uvn + vec2( x - 4.0, 0.0 ) * 7E-3 ).z
    ) * 0.1;
  }
  col *= 0.6;

  // ac beat
  col *= 1.0 + clamp( noise( vec2( 0.0, uv.y + time * 0.2 ) ) * 0.6 - 0.25, 0.0, 0.1 );

  gl_FragColor = vec4( col, 1.0 );
  gl_FragColor = vec4( col, 1.0 - col.r );
}

#undef gl_FragCoord
#undef gl_FragColor
```

## RTX filter for your game (brightness, ripples and other effects)
https://godotshaders.com/shader/rtx-filter-for-your-game-brightness-ripples-and-other-effects/
![[シェーダー-1746097473633.png|640x357]]
![[シェーダー-1746097542568.png|640x183]]
```python
shader_type canvas_item;

uniform sampler2D SCREEN_TEXTURE : hint_screen_texture, filter_linear_mipmap;
uniform float brightness : hint_range(0.0, 2.0) = 1.0;
uniform float contrast : hint_range(0.0, 2.0) = 1.0;
uniform float saturation : hint_range(0.0, 2.0) = 1.0;
uniform float gamma : hint_range(0.1, 2.2) = 1.0;
uniform float bloom_intensity : hint_range(0.0, 1.0) = 0.2;
uniform float bloom_threshold : hint_range(0.0, 1.0) = 0.6;
uniform float bloom_blur : hint_range(0.0, 10.0) = 4.0;
uniform vec4 tint_color : source_color = vec4(1.0, 0.97, 0.9, 1.0);
uniform float tint_strength : hint_range(0.0, 1.0) = 0.2;
uniform float vignette_intensity : hint_range(0.0, 1.0) = 0.2;
uniform float vignette_opacity : hint_range(0.0, 1.0) = 0.5;
uniform float vignette_roundness : hint_range(0.0, 2.0) = 1.0;
uniform vec4 vignette_color : source_color = vec4(0.0, 0.0, 0.0, 1.0);
uniform float film_grain : hint_range(0.0, 0.5) = 0.03;
uniform float film_grain_speed : hint_range(0.0, 10.0) = 1.0;
uniform float chromatic_aberration : hint_range(0.0, 5.0) = 0.5;
uniform bool enable_god_rays = true;
uniform float god_rays_intensity : hint_range(0.0, 1.0) = 0.1;
uniform vec2 light_position = vec2(0.5, 0.5);
uniform bool enable_lens_dirt = true;
uniform sampler2D lens_dirt_texture : hint_default_white;
uniform float lens_dirt_intensity : hint_range(0.0, 1.0) = 0.5;
uniform bool enable_color_grading = true;
uniform sampler2D color_lut : hint_default_white;
uniform float color_lut_mix : hint_range(0.0, 1.0) = 0.8;
uniform bool enable_dithering = true;
uniform float dithering_intensity : hint_range(0.0, 0.1) = 0.02;
uniform bool enable_scanlines = false;
uniform float scanlines_opacity : hint_range(0.0, 1.0) = 0.2;
uniform float scanlines_width : hint_range(1.0, 20.0) = 10.0;
uniform bool enable_sharpen = true;
uniform float sharpen_intensity : hint_range(0.0, 5.0) = 0.5;
uniform bool enable_color_blindness = false;
uniform int color_blindness_type : hint_range(0, 2) = 0;
uniform float color_blindness_intensity : hint_range(0.0, 1.0) = 1.0;
uniform bool enable_pixelate = false;
uniform float pixelate_amount : hint_range(1.0, 512.0) = 64.0;
uniform bool enable_edge_detection = false;
uniform float edge_detection_intensity : hint_range(0.0, 5.0) = 1.0;
uniform vec4 edge_detection_color : source_color = vec4(0.0, 0.0, 0.0, 1.0);
uniform bool enable_night_vision = false;
uniform vec4 night_vision_color : source_color = vec4(0.0, 1.0, 0.0, 1.0);
uniform float night_vision_noise : hint_range(0.0, 1.0) = 0.3;

vec3 rgb_to_hsv(vec3 rgb) {
    float cmax = max(rgb.r, max(rgb.g, rgb.b));
    float cmin = min(rgb.r, min(rgb.g, rgb.b));
    float delta = cmax - cmin;
    
    vec3 hsv = vec3(0.0, 0.0, cmax);
    
    if (delta > 0.0001) {
        hsv.y = delta / cmax;
        
        if (abs(cmax - rgb.r) < 0.0001) {
            hsv.x = (rgb.g - rgb.b) / delta + (rgb.g < rgb.b ? 6.0 : 0.0);
        } else if (abs(cmax - rgb.g) < 0.0001) {
            hsv.x = (rgb.b - rgb.r) / delta + 2.0;
        } else {
            hsv.x = (rgb.r - rgb.g) / delta + 4.0;
        }
        
        hsv.x /= 6.0;
    }
    
    return hsv;
}

vec3 hsv_to_rgb(vec3 hsv) {
    if (abs(hsv.y) < 0.0001) {
        return vec3(hsv.z);
    }
    
    float h = hsv.x * 6.0;
    int i = int(floor(h));
    float f = h - float(i);
    float p = hsv.z * (1.0 - hsv.y);
    float q = hsv.z * (1.0 - hsv.y * f);
    float t = hsv.z * (1.0 - hsv.y * (1.0 - f));
    
    if (i == 0) return vec3(hsv.z, t, p);
    else if (i == 1) return vec3(q, hsv.z, p);
    else if (i == 2) return vec3(p, hsv.z, t);
    else if (i == 3) return vec3(p, q, hsv.z);
    else if (i == 4) return vec3(t, p, hsv.z);
    else return vec3(hsv.z, p, q);
}

vec3 blur(sampler2D tex, vec2 uv, vec2 resolution, float radius) {
    vec3 color = vec3(0.0);
    float total = 0.0;
    
    for (float x = -radius; x <= radius; x += 1.0) {
        for (float y = -radius; y <= radius; y += 1.0) {
            vec2 offset = vec2(x, y) / resolution;
            float weight = 1.0 - length(offset) / radius;
            if (weight < 0.0) weight = 0.0;
            color += texture(tex, uv + offset).rgb * weight;
            total += weight;
        }
    }
    
    return color / total;
}

float random(vec2 uv) {
    return fract(sin(dot(uv, vec2(12.9898, 78.233))) * 43758.5453123);
}

vec3 god_rays(sampler2D tex, vec2 uv, vec2 light_pos, float exposure) {
    vec3 color = vec3(0.0);
    float decay = 0.97;
    float density = 0.5;
    float weight = 0.3;
    
    vec2 delta = (uv - light_pos) * density / 100.0;
    vec2 pos = uv;
    
    for (int i = 0; i < 100; i++) {
        pos -= delta;
        vec3 sample_color = texture(tex, pos).rgb;
        sample_color *= weight * exposure;
        color += sample_color;
        weight *= decay;
    }
    
    return color;
}

float apply_dither_effect(vec2 uv, float color_value) {
    float bayer[16] = float[16](
        0.0, 8.0, 2.0, 10.0,
        12.0, 4.0, 14.0, 6.0,
        3.0, 11.0, 1.0, 9.0,
        15.0, 7.0, 13.0, 5.0
    );
    
    int x = int(mod(uv.x, 4.0));
    int y = int(mod(uv.y, 4.0));
    float dither_value = bayer[y * 4 + x] / 16.0 - 0.5;
    
    return color_value + dither_value * dithering_intensity;
}

vec3 apply_color_blindness(vec3 color, int type, float intensity) {
    mat3 protanopia = mat3(
        vec3(0.567, 0.433, 0.0),
        vec3(0.558, 0.442, 0.0),
        vec3(0.0, 0.242, 0.758)
    );
    
    mat3 deuteranopia = mat3(
        vec3(0.625, 0.375, 0.0),
        vec3(0.7, 0.3, 0.0),
        vec3(0.0, 0.3, 0.7)
    );
    
    mat3 tritanopia = mat3(
        vec3(0.95, 0.05, 0.0),
        vec3(0.0, 0.433, 0.567),
        vec3(0.0, 0.475, 0.525)
    );
    
    mat3 matrix;
    if (type == 0) matrix = protanopia;
    else if (type == 1) matrix = deuteranopia;
    else matrix = tritanopia;
    
    vec3 blind_color = matrix * color;
    return mix(color, blind_color, intensity);
}

float edge_detection(sampler2D tex, vec2 uv, vec2 texel_size) {
    float offset = 1.0;
    
    vec3 a = texture(tex, uv + texel_size * vec2(-offset, -offset)).rgb;
    vec3 b = texture(tex, uv + texel_size * vec2(0.0, -offset)).rgb;
    vec3 c = texture(tex, uv + texel_size * vec2(offset, -offset)).rgb;
    vec3 d = texture(tex, uv + texel_size * vec2(-offset, 0.0)).rgb;
    vec3 f = texture(tex, uv + texel_size * vec2(offset, 0.0)).rgb;
    vec3 g = texture(tex, uv + texel_size * vec2(-offset, offset)).rgb;
    vec3 h = texture(tex, uv + texel_size * vec2(0.0, offset)).rgb;
    vec3 i = texture(tex, uv + texel_size * vec2(offset, offset)).rgb;
    
    vec3 sobel_x = -a - 2.0 * d - g + c + 2.0 * f + i;
    vec3 sobel_y = -a - 2.0 * b - c + g + 2.0 * h + i;
    
    float edge = length(sobel_x) + length(sobel_y);
    return edge;
}

vec3 sharpen(sampler2D tex, vec2 uv, vec2 texel_size, float intensity) {
    vec3 center = texture(tex, uv).rgb;
    vec3 top = texture(tex, uv + vec2(0.0, -texel_size.y)).rgb;
    vec3 bottom = texture(tex, uv + vec2(0.0, texel_size.y)).rgb;
    vec3 left = texture(tex, uv + vec2(-texel_size.x, 0.0)).rgb;
    vec3 right = texture(tex, uv + vec2(texel_size.x, 0.0)).rgb;
    
    vec3 sharpen_color = center * (1.0 + 4.0 * intensity) - (top + bottom + left + right) * intensity;
    return sharpen_color;
}

vec3 apply_lut(sampler2D lut_texture, vec3 color) {
    float blue = color.b * 63.0;
    vec2 quad1 = vec2(0.0);
    quad1.y = floor(floor(blue) / 8.0);
    quad1.x = floor(blue) - (quad1.y * 8.0);
    
    vec2 quad2 = vec2(0.0);
    quad2.y = floor(ceil(blue) / 8.0);
    quad2.x = ceil(blue) - (quad2.y * 8.0);
    
    vec2 texPos1 = vec2(0.0);
    texPos1.x = (quad1.x * 0.125) + 0.0625 + (0.125 * color.r);
    texPos1.y = (quad1.y * 0.125) + 0.0625 + (0.125 * color.g);
    
    vec2 texPos2 = vec2(0.0);
    texPos2.x = (quad2.x * 0.125) + 0.0625 + (0.125 * color.r);
    texPos2.y = (quad2.y * 0.125) + 0.0625 + (0.125 * color.g);
    
    vec3 newColor1 = texture(lut_texture, texPos1).rgb;
    vec3 newColor2 = texture(lut_texture, texPos2).rgb;
    
    vec3 newColor = mix(newColor1, newColor2, fract(blue));
    return newColor;
}

void fragment() {
    vec2 uv = SCREEN_UV;
    vec2 pixel_size = 1.0 / SCREEN_PIXEL_SIZE;
    
    if (enable_pixelate) {
        vec2 pixelated_uv = floor(uv * pixelate_amount) / pixelate_amount;
        uv = pixelated_uv;
    }
    
    vec3 color;
    if (chromatic_aberration > 0.0001) {
        vec2 direction = (uv - 0.5) * 2.0;
        vec2 distortion = direction * chromatic_aberration / 1000.0;
        
        color.r = texture(SCREEN_TEXTURE, uv - distortion).r;
        color.g = texture(SCREEN_TEXTURE, uv).g;
        color.b = texture(SCREEN_TEXTURE, uv + distortion).b;
    } else {
        color = texture(SCREEN_TEXTURE, uv).rgb;
    }
    
    if (enable_sharpen) {
        color = sharpen(SCREEN_TEXTURE, uv, 1.0 / pixel_size, sharpen_intensity);
    }
    
    if (bloom_intensity > 0.0001) {
        vec3 bloom_color = blur(SCREEN_TEXTURE, uv, pixel_size, bloom_blur);
        bloom_color = max(bloom_color - bloom_threshold, 0.0);
        color += bloom_color * bloom_intensity;
        
        if (enable_lens_dirt && textureSize(lens_dirt_texture, 0).x > 1) {
            vec3 dirt = texture(lens_dirt_texture, uv).rgb;
            color += bloom_color * dirt * lens_dirt_intensity;
        }
    }
    
    if (enable_god_rays) {
        vec3 rays = god_rays(SCREEN_TEXTURE, uv, light_position, god_rays_intensity * 0.1);
        color += rays;
    }
    
    if (enable_edge_detection) {
        float edge = edge_detection(SCREEN_TEXTURE, uv, 1.0 / pixel_size);
        color = mix(color, edge_detection_color.rgb, edge * edge_detection_intensity);
    }
    
    color = (color - 0.5) * contrast + 0.5;
    color *= brightness;
    
    vec3 hsv = rgb_to_hsv(color);
    hsv.y *= saturation;
    color = hsv_to_rgb(hsv);
    
    color = pow(color, vec3(1.0 / gamma));
    
    color = mix(color, color * tint_color.rgb, tint_strength);
    
    if (enable_color_grading && textureSize(color_lut, 0).x > 1) {
        vec3 graded_color = apply_lut(color_lut, color);
        color = mix(color, graded_color, color_lut_mix);
    }
    
    if (vignette_intensity > 0.0001) {
        vec2 vignette_uv = uv * (1.0 - uv.yx);
        float vig = vignette_uv.x * vignette_uv.y * 15.0;
        vig = pow(vig, vignette_roundness);
        color = mix(color, mix(color, vignette_color.rgb, vignette_opacity), vignette_intensity * (1.0 - vig));
    }
    
    if (film_grain > 0.0001) {
        float grain = random(uv + vec2(TIME * film_grain_speed * 0.01, TIME * film_grain_speed * 0.01)) * film_grain;
        color += grain - film_grain/2.0;
    }
    
    if (enable_scanlines) {
        float scanline = sin(uv.y * scanlines_width * pixel_size.y) * 0.5 + 0.5;
        scanline = pow(scanline, 1.0) * scanlines_opacity;
        color = mix(color, color * (1.0 - scanline), scanlines_opacity);
    }
    
    if (enable_color_blindness) {
        color = apply_color_blindness(color, color_blindness_type, color_blindness_intensity);
    }
    
    if (enable_night_vision) {
        float luminance = dot(color, vec3(0.299, 0.587, 0.114));
        vec3 night_color = vec3(luminance) * night_vision_color.rgb;
        
        float noise = random(uv + vec2(TIME * 0.1, TIME * 0.1)) * night_vision_noise;
        night_color += vec3(noise);
        
        float scanline = sin(uv.y * pixel_size.y * 200.0) * 0.1 + 0.9;
        night_color *= scanline;
        
        color = night_color;
    }
    
    if (enable_dithering) {
        color.r = apply_dither_effect(FRAGCOORD.xy, color.r);
        color.g = apply_dither_effect(FRAGCOORD.xy, color.g);
        color.b = apply_dither_effect(FRAGCOORD.xy, color.b);
    }
    
    COLOR = vec4(color, 1.0);
}
```

## Edge Detection (Sobel Filter and Gaussian Blur)
https://godotshaders.com/shader/edge-detection-sobel-filter-and-gaussian-blur/
![[シェーダー-1746097831315.png|640x360]]
```python
shader_type canvas_item;
render_mode unshaded;

uniform sampler2D SCREEN_TEXTURE: hint_screen_texture, repeat_disable, filter_nearest;

vec3 convolution(sampler2D tex, vec2 uv, vec2 pixel_size) {
	vec3 conv = vec3(0.0);
	// Gaussian blur kernel
	float gauss[25] = {
		0.00390625, 0.015625, 0.0234375, 0.015625, 0.00390625,
		0.015625, 0.0625, 0.09375, 0.0625, 0.015625, 0.0234375,
		0.09375, 0.140625, 0.09375, 0.0234375, 0.015625,
		0.0625, 0.09375, 0.0625, 0.015625, 0.00390625,
		0.015625, 0.0234375, 0.015625, 0.00390625
	};
	for (int row = 0; row < 5; row++) {
		for (int col = 0; col < 5; col++) {
			conv += texture(tex, uv + vec2(float(col - 2), float(row - 2)) * pixel_size).rgb * gauss[row * 5 + col];
		}
	}
	return conv;
}

void fragment() {
	vec3 pixels[9];  // Sobel kernel
	// [0, 1, 2]
	// [3, 4, 5]
	// [6, 7, 8]
	for (int row = 0; row < 3; row++) {
		for (int col = 0; col < 3; col++) {
			vec2 uv = SCREEN_UV + vec2(float(col - 1), float(row - 1)) * SCREEN_PIXEL_SIZE;
			pixels[row * 3 + col] = convolution(SCREEN_TEXTURE, uv, SCREEN_PIXEL_SIZE);
		}
	}

	// Sobel operator
	vec3 gx = (
		pixels[0] * -1.0 + pixels[3] * -2.0 + pixels[6] * -1.0
		+ pixels[2] * 1.0 + pixels[5] * 2.0 + pixels[8] * 1.0
	);
	vec3 gy = (
		pixels[0] * -1.0 + pixels[1] * -2.0 + pixels[2] * -1.0
		+ pixels[6] * 1.0 + pixels[7] * 2.0 + pixels[8] * 1.0
	);
	vec3 sobel = sqrt(gx * gx + gy * gy);
	COLOR = vec4(sobel, 1.0);
}
```

## Normal-based Edge Detection with Sobel Operator -Screenspace
https://godotshaders.com/shader/normal-based-edge-detection-with-sobel-operator-screenspace/
![[シェーダー-1746097932414.png|640x360]]
```python
shader_type spatial;
render_mode unshaded;

uniform sampler2D SCREEN_TEXTURE: hint_screen_texture, filter_linear_mipmap;
uniform sampler2D NORMAL_TEXTURE : hint_normal_roughness_texture, filter_linear_mipmap;

uniform float edge_threshold = 0.5;
uniform vec3 line_color: source_color = vec3(0.043, 0.282, 0.467);
uniform vec3 background_color: source_color = vec3(0.408, 0.969, 0.694);

const mat3 sobel_y = mat3(
	vec3(1.0, 0.0, -1.0),
	vec3(2.0, 0.0, -2.0),
	vec3(1.0, 0.0, -1.0)
);

const mat3 sobel_x = mat3(
	vec3(1.0, 2.0, 1.0),
	vec3(0.0, 0.0, 0.0),
	vec3(-1.0, -2.0, -1.0)
);

vec3 normalize_normal(vec3 normal){
	return normalize(normal * 2.0 - 1.0);
}

void vertex(){
	POSITION = vec4(VERTEX.xy, 1.0, 1.0);
}

void fragment() {
	vec2 uv = SCREEN_UV;
	vec4 screen_color = texture(SCREEN_TEXTURE, uv);
	vec2 offset = 0.5 / VIEWPORT_SIZE;
	vec3 normal = normalize_normal(texture(NORMAL_TEXTURE, uv).rgb);
	
	vec3 n = normalize_normal(texture(NORMAL_TEXTURE, uv + vec2(0.0, -offset.y)).rgb);
	vec3 s = normalize_normal(texture(NORMAL_TEXTURE, uv + vec2(0.0, offset.y)).rgb);
	vec3 e = normalize_normal(texture(NORMAL_TEXTURE, uv + vec2(offset.x, 0.0)).rgb);
	vec3 w = normalize_normal(texture(NORMAL_TEXTURE, uv + vec2(-offset.x, 0.0)).rgb);
	vec3 nw = normalize_normal(texture(NORMAL_TEXTURE, uv + vec2(-offset.x, -offset.y)).rgb);
	vec3 ne = normalize_normal(texture(NORMAL_TEXTURE, uv + vec2(offset.x, -offset.y)).rgb);
	vec3 sw = normalize_normal(texture(NORMAL_TEXTURE, uv + vec2(-offset.x, offset.y)).rgb);
	vec3 se = normalize_normal(texture(NORMAL_TEXTURE, uv + vec2(offset.x, offset.y)).rgb);
	
	mat3 surrounding_pixels = mat3(
		vec3(length(nw-normal), length(n-normal), length(ne-normal)),
		vec3(length(w-normal), length(normal-normal), length(e-normal)),
		vec3(length(sw-normal), length(s-normal), length(se-normal))
	);
	
	float edge_x = dot(sobel_x[0], surrounding_pixels[0]) + dot(sobel_x[1], surrounding_pixels[1]) + dot(sobel_x[2], surrounding_pixels[2]);
	float edge_y = dot(sobel_y[0], surrounding_pixels[0]) + dot(sobel_y[1], surrounding_pixels[1]) + dot(sobel_y[2], surrounding_pixels[2]);
	
	float edge = sqrt(pow(edge_x, 2.0)+pow(edge_y, 2.0));
	
	if (edge > edge_threshold) {
		ALBEDO = line_color;
	} else {
		ALBEDO = background_color;
	}
}
```

## Noise Offset (Wiggle)
https://godotshaders.com/shader/noise-offset-wiggle/
![[シェーダー-1746097978590.png|640x360]]
```python
shader_type canvas_item;

uniform sampler2D SCREEN_TEXTURE: hint_screen_texture, filter_linear;
uniform sampler2D NOISE_TEXTURE: repeat_enable;
uniform float strength: hint_range(0.0, 5, 0.1) = 1.0;
uniform float uv_scaling: hint_range (0.0, 1.0, 0.05) = 1.0;
uniform vec2 movement_direction = vec2(1, 0);
uniform float movement_speed: hint_range (0.0, 0.5, 0.01) = 0.1;

void fragment() {
	vec2 uv = SCREEN_UV;
	vec4 screen_texture = texture(SCREEN_TEXTURE, uv);
	vec2 movement_factor = movement_direction * movement_speed * TIME;
	float noise_value = texture(NOISE_TEXTURE, uv*uv_scaling + movement_factor).r - 0.5;
	uv += noise_value * SCREEN_PIXEL_SIZE * strength;
	COLOR = texture(SCREEN_TEXTURE, uv);
}
```

## Chromatic Abberation (With Offset)：色収差RGBずらし
https://godotshaders.com/shader/chromatic-abberation-with-offset/
![[シェーダー-1746098090198.png|640x360]]
```python
shader_type canvas_item;

uniform sampler2D offset_image : hint_white;
uniform float strength = 1.0;

void fragment() {
	vec4 output = texture(SCREEN_TEXTURE, SCREEN_UV);
	
	float shift = strength * texture(offset_image, SCREEN_UV).r / 100.0;
	output.r = texture(SCREEN_TEXTURE, vec2(SCREEN_UV.x + shift, SCREEN_UV.y)).r;
	output.g = texture(SCREEN_TEXTURE, SCREEN_UV).g;
	output.b = texture(SCREEN_TEXTURE, vec2(SCREEN_UV.x - shift, SCREEN_UV.y)).b;
	
	COLOR = output;
}
```

## Tilt-Shift Shader (minimal)
https://godotshaders.com/shader/tilt-shift-shader-minimal/
![[シェーダー-1746098209734.png|640x360]]
```python
// Original by ChaffDave : https://godotshaders.com/shader/tilt-shift-shader/

shader_type canvas_item;

uniform float limit: hint_range(0.0,0.5) = 0.2;
uniform float blur: hint_range(0.0,8.0) = 2.0;
uniform sampler2D screen_texture : hint_screen_texture, filter_linear_mipmap;

void fragment(){
	
	if (UV.y<limit){

		float blur_amount = blur * (1.0 - (SCREEN_UV.y / limit));
		COLOR = textureLod(screen_texture, SCREEN_UV, blur_amount);

	} else if (UV.y > 1.0-limit){

		float blur_amount = blur * (1.0 - ((1.0 - SCREEN_UV.y) / limit));
		COLOR = textureLod(screen_texture, SCREEN_UV, blur_amount);

	} else {
		COLOR.a = 0.0;
	}
}
```

## Perfect Retro Pixel Shader – Godot 4
https://godotshaders.com/shader/perfect-retro-pixel-shader-godot-4/
![[シェーダー-1746098281304.png|640x360]]
```python
shader_type canvas_item;

uniform float shake: hint_range(0, 10) = 0.015;
uniform float noiseQuality: hint_range(0.1, 250, 0.1) = 250;
uniform float noiseIntensity: hint_range(0, 0.05, 0.001) = 0.001;
uniform float offsetIntensity: hint_range(0, 0.05, 0.0001) = 0.0045;
uniform float colorOffsetIntensity: hint_range(0.1, 1.5, 0.001) = 0.2;
uniform float pixelSize: hint_range(1, 1024) = 200.0;
uniform float grainIntensity: hint_range(0, 1) = 0.04; // Intensity of film grain

uniform sampler2D screen_texture : hint_screen_texture, repeat_disable, filter_nearest;
uniform sampler2D overlay_texture : hint_default_transparent;
uniform bool use_overlay = true; // Toggle for using overlay texture
uniform bool use_lens_distortion = true; // Toggle for using lens distortion

uniform vec4 overlay_color : source_color = vec4(1.0, 1.0, 1.0, 10.0); // Allow changing overlay color
uniform float lens_distortion_strength: hint_range(0, 0.1) = 0.05; // Strength of lens distortion effect

varying vec2 previous_pos; // Previous fragment position

float rand(vec2 co)
{
    return fract(sin(dot(co.xy, vec2(12.9898, 78.233))) * 43758.5453);
}

float verticalBar(float pos, float uvY, float offset)
{
    float edge0 = (pos - shake);
    float edge1 = (pos + shake);

    float x = smoothstep(edge0, pos, uvY) * offset;
    x -= smoothstep(pos, edge1, uvY) * offset;
    return x;
}

void fragment() {
    vec2 iResolution = 1.0 / SCREEN_PIXEL_SIZE;
    vec2 uv = FRAGCOORD.xy / iResolution.xy;

    // Calculate velocity
    vec2 velocity = uv - previous_pos;

    // Apply pixelation effect
    uv = floor(uv * pixelSize) / pixelSize;

    // Add vertical bars distortion
    for (float i = 0.0; i < 0.71; i += 0.1313)
    {
        float d = mod(TIME * i, 1.7);
        float o = sin(1.0 - tan(TIME * 0.24 * i));
        o *= offsetIntensity;
        uv.x += verticalBar(d, uv.y, o);
    }

    // Add noise distortion
    float uvY = uv.y * noiseQuality;
    uvY = float(int(uvY)) * (1.0 / noiseQuality);
    float noise = rand(vec2(TIME * 0.00001, uvY));
    uv.x += noise * noiseIntensity;

    // Add color offset distortion
    vec2 offsetR = vec2(0.006 * sin(TIME), 0.0) * colorOffsetIntensity;
    vec2 offsetG = vec2(0.0073 * cos(TIME * 0.97), 0.0) * colorOffsetIntensity;

    float r = texture(screen_texture, uv + offsetR).r;
    float g = texture(screen_texture, uv + offsetG).g;
    float b = texture(screen_texture, uv).b;

    vec4 tex = vec4(r, g, b, 1.0);

    // Overlay with an optional texture
    if (use_overlay) {
        vec4 overlayColor = texture(overlay_texture, uv) * overlay_color; // Apply overlay color
        tex = mix(tex, overlayColor, overlayColor.a);
    }

    // Add film grain
    float grain = rand(uv) * 2.0 - 1.0; // Range from -1 to 1
    tex.rgb += grainIntensity * grain;

    // Apply lens distortion
    if (use_lens_distortion) {
        vec2 distortion = uv * lens_distortion_strength * (uv - 1.0);
        vec4 distorted_tex = texture(screen_texture, uv + distortion);
        tex = mix(tex, distorted_tex, 0.5);
    }

    // Apply final color
    COLOR = tex;

    // Store current position for next frame
    previous_pos = FRAGCOORD.xy / iResolution.xy;
}

```

## Pixel Quantization
https://godotshaders.com/shader/pixel-quantization/
![[シェーダー-1746098669349.png|640x360]]
```python
/*
 * This should be your main shader file if using with a ColorRect.
*/

shader_type canvas_item;
// This file relies on a quantize shader include file, listed further down.
// The paths must match your file's location.
#include "res://shaders/quantize.gdshaderinc"
uniform sampler2D SCREEN_TEXTURE : hint_screen_texture, repeat_disable, filter_nearest;

void vertex() {
    g_q_size = getQuantizeSize(CANVAS_MATRIX);
    g_model_matrix = MODEL_MATRIX;
    g_world_to_clip = SCREEN_MATRIX * CANVAS_MATRIX;
    g_vertex = VERTEX;
}

void fragment() {
	COLOR = quantizeScreen(SCREEN_TEXTURE, SCREEN_UV, g_vertex);
}

/*
 * This should be your main shader file if using with a Sprite2D.
*/

shader_type canvas_item;
// This file relies on a quantize shader include file, listed further down.
// The paths must match your file's location.
#include "res://shaders/quantize.gdshaderinc"

void vertex() {
    g_q_size = getQuantizeSize(CANVAS_MATRIX);
    g_model_matrix = MODEL_MATRIX;
    g_texture_size = 1. / TEXTURE_PIXEL_SIZE;
    g_vertex = VERTEX;
    g_flat_vertex = VERTEX;
}

void fragment() {
	COLOR = quantizeTexture(TEXTURE, UV);
}

/*
 * This is the quantize shader-include file. Please make a copy in a
 * separate file, and update the #include in the file you plan to reference it in.
*/

group_uniforms Quantization;
uniform float quantize_size : hint_range(0,100, 1.0) = 1;
uniform bool handle_scale;
uniform bool handle_subpixels = true;
group_uniforms;

varying mat4 g_model_matrix;
varying mat4 g_world_to_clip;
varying vec2 g_texture_size;
varying vec2 g_vertex;
varying flat vec2 g_flat_vertex;
varying float g_q_size;

float getQuantizeSize(mat4 canvas_matrix) {
    vec2 g_zoom = vec2(length(canvas_matrix[0].xyz), length(canvas_matrix[1].xyz));
    float q_size = quantize_size;

    if (handle_subpixels && g_zoom.x < 1.) {
        q_size = round(quantize_size * (1. / g_zoom.x));
    }

    return q_size;
}

vec4 quantizeTexture(sampler2D in_texture,vec2 in_uv) {
    if (g_q_size == 0.) {
        return texture(in_texture, in_uv);
    }

    float q_size = g_q_size;

    // gets -1 if not flipped, 1 if flipped
    vec2 flipped = sign(g_flat_vertex - g_vertex);
    // gets the offset of the texture
    // adds 1 when flipped
    vec2 offset = g_flat_vertex / g_texture_size;

    vec2 uv = in_uv;
    // add offset so positioned back at 0,0 locally
    // so grid lines up
    uv *= flipped;
    uv = offset - uv;
    // scale to texture size
    uv *= g_texture_size;

    if (handle_scale) { // convert to world space
        uv = (g_model_matrix * vec4(uv, 0, 1)).xy;
    }

    // quantize to specified pixel size
    uv /= q_size;
    uv = floor(uv) + 0.5;
    uv *= q_size;

    if (handle_scale) { // convert back to local space
        uv = (inverse(g_model_matrix) * vec4(uv, 0, 1)).xy;
    }

    // normalize
    uv /= g_texture_size;
    // remove offset so pixel taken at correct location
    uv = offset - uv;
    uv *= flipped;
    return texture(in_texture, uv);
}

vec4 quantizeScreen(sampler2D in_screen_texture, vec2 in_screen_uv, vec2 in_vertex) {
    if (g_q_size == 0.) {
        return texture(in_screen_texture, in_screen_uv);
    }

    vec2 uv = (g_model_matrix * vec4(in_vertex, 0, 1)).xy;
    uv /= g_q_size;
    uv = floor(uv) + 0.5;
    uv *= g_q_size;
    uv = (g_world_to_clip * vec4(uv, 0, 1)).xy;
    uv = uv * 0.5 + 0.5;
    return texture(in_screen_texture, uv);
}
```

## Pixel Art Wind Sway (MeshInstance3D, Godot 4.0)
https://godotshaders.com/shader/pixel-art-wind-sway-meshinstance3d-godot-4-0/
![[シェーダー-1746098428096.png|640x360]]
```python
shader_type spatial;
render_mode cull_disabled, depth_draw_opaque;

uniform sampler2D grass_texture : filter_nearest, source_color;

void vertex(){
	NORMAL = vec3(0.0, 1.0, 0.0);
	VERTEX.x += sin(NODE_POSITION_WORLD.x + TIME * 1.25 + UV.y) * ( 1.0 - UV.y) * 0.2;
	VERTEX.z += cos(NODE_POSITION_WORLD.z + TIME * 0.45 + UV.y) * ( 1.0 - UV.y) * 0.15;
}

void fragment(){
	ALBEDO = texture(grass_texture, UV).rgb;
	ALPHA = texture(grass_texture, UV).a;
}
```

## Billboard Sprite3D Hitflash (Godot 4.x)
https://godotshaders.com/shader/billboard-sprite3d-hitflash-godot-4-x/
![[シェーダー-1746098509545.png|640x360]]
```python
shader_type spatial;
render_mode unshaded, cull_disabled, depth_draw_opaque, depth_prepass_alpha;
// Remove unshaded if you want shading
// Replace cull_disabled if you want to only show one side (cull_front, cull_back)
// Reference for depth ::: https://docs.godotengine.org/en/stable/tutorials/shaders/shader_reference/spatial_shader.html#render-modes

uniform bool active = false; // Sets pixels to flash color when true
uniform vec4 flash_color: source_color = vec4(1.0,1.0,1.0,1.0); // The color to use when active
uniform bool y_billboard = false; // Enables Y-Billboarding when true
uniform sampler2D tex: source_color; // A copy of the texture for the Sprite3D or AnimatedSprite3D 

void vertex() {
	// You can comment out this function if you don't need billboarding
	if (y_billboard == true) {
		MODELVIEW_MATRIX = VIEW_MATRIX * mat4(vec4(normalize(cross(vec3(0.0, 1.0, 0.0), INV_VIEW_MATRIX[2].xyz)), 0.0), vec4(0.0, 1.0, 0.0, 0.0), vec4(normalize(cross(INV_VIEW_MATRIX[0].xyz, vec3(0.0, 1.0, 0.0))), 0.0), MODEL_MATRIX[3]);
	}
	else {
		MODELVIEW_MATRIX = VIEW_MATRIX * mat4(INV_VIEW_MATRIX[0], INV_VIEW_MATRIX[1], INV_VIEW_MATRIX[2], MODEL_MATRIX[3]);
	}
	MODELVIEW_NORMAL_MATRIX = mat3(MODELVIEW_MATRIX);
}

void fragment() {
	vec4 col = texture(tex,UV);
	vec4 set_col = col;
	if(active == true) {
		set_col = flash_color;
	}
	ALBEDO = vec3(set_col.r,set_col.g,set_col.b);
	ALPHA = col.a;
}
```

