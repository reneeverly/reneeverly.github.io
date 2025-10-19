---
layout: standard
title: CSS Named Colors Sorted by Oklab Perceived Lightness
---
## {{ page.title }}
<time datetime="{{ page.date }}">{{ page.date | date: "%Y-%m-%d" }}</time>

My quest here was to figure out if any of the named CSS colors can be used as convenient `white` or `black` alternatives for a less harsh, yet still WCAG AAA and desaturated palette.  The answer is that there are no suitable dark colors, but there are a few potentially usefull light colors.  I personally think that `snow` and `floralwhite` are the most neutral replacements for `white`.

### References
* <https://www.w3.org/wiki/CSS/Properties/color/keywords>
* <https://bottosson.github.io/posts/oklab/>
* <https://bottosson.github.io/posts/colorpicker/>
* <https://drafts.csswg.org/css-color/#valdef-color-rebeccapurple>

### Appendix A: Dynamically Generated Table
<noscript>Dynamic table generation requires javascript.</noscript>
<table>
<thead><tr><th>Swatch</th><th>Name</th><th>Oklab Lightness</th><!--<th>OKHSL Saturation</th>--></tr></thead>
<tbody id="dynamic_color_table"></tbody>
</table>
<script>
/*
Copyright (c) 2021 Björn Ottosson

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
of the Software, and to permit persons to whom the Software is furnished to do
so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
*/
function rgb_to_hsl(r, g, b)
{
    r /= 255;
    g /= 255;
    b /= 255;

    let max = Math.max(r, g, b);
    let min = Math.min(r, g, b);
    let h, s;
    let l = (max + min) / 2;

    if (max == min)
    {
        h = s = 0;
    }
    else
    {
        let d = max - min;
        s = l > 0.5 ? d / (2 - max - min) : d / (max + min);
        switch(max)
        {
            case r:
                h = (g - b) / d + (g < b ? 6 : 0);
                break;
            case g:
                h = (b - r) / d + 2;
                break;
            case b:
                h = (r - g) / d + 4;
                break;
        }
        h /= 6;
    }

    return [h, s, l];
}

function hsl_to_rgb(h, s, l)
{
    let r, g, b;

    if (s == 0)
    {
        r = g = b = l;
    }
    else
    {
        function hue_to_rgb(p, q, t)
        {
            if (t < 0)
                t += 1;
            if (t > 1)
                t -= 1;
            if (t < 1/6)
                return p + (q - p) * 6 * t;
            if (t < 1/2)
                return q;
            if (t < 2/3)
                return p + (q - p) * (2/3 - t) * 6;
            return p;
        }

        let q = l < 0.5 ? l * (1 + s) : l + s - l * s;
        let p = 2 * l - q;
        r = hue_to_rgb(p, q, h + 1/3);
        g = hue_to_rgb(p, q, h);
        b = hue_to_rgb(p, q, h - 1/3);
    }

    return [r * 255, g * 255, b * 255];
}

function rgb_to_hsv(r, g, b)
{
    r = r/255,
    g = g/255,
    b = b/255;

    let max = Math.max(r, g, b);
    let min = Math.min(r, g, b);
    let h, s;
    let v = max;

    let d = max - min;
    s = max == 0 ? 0 : d / max;

    if (max == min)
    {
        h = 0; // achromatic
    }
    else
    {
        switch(max){
            case r:
                h = (g - b) / d + (g < b ? 6 : 0);
                break;
            case g:
                h = (b - r) / d + 2;
                break;
            case b:
                h = (r - g) / d + 4;
                break;
        }
        h /= 6;
    }

    return [h, s, v];
}

function hsv_to_rgb(h, s, v){
    let r, g, b;

    let i = Math.floor(h * 6);
    let f = h * 6 - i;
    let p = v * (1 - s);
    let q = v * (1 - f * s);
    let t = v * (1 - (1 - f) * s);

    switch(i % 6){
        case 0:
            r = v;
            g = t;
            b = p;
            break;
        case 1:
            r = q;
            g = v;
            b = p;
            break;
        case 2:
            r = p;
            g = v;
            b = t;
            break;
        case 3:
            r = p;
            g = q;
            b = v;
            break;
        case 4:
            r = t;
            g = p;
            b = v;
            break;
        case 5:
            r = v;
            g = p;
            b = q;
            break;
    }

    return [r * 255, g * 255, b * 255];
}

function srgb_transfer_function(a) {
    return .0031308 >= a ? 12.92 * a : 1.055 * Math.pow(a, .4166666666666667) - .055
}

function srgb_transfer_function_inv(a) {
    return .04045 < a ? Math.pow((a + .055) / 1.055, 2.4) : a / 12.92
}

function linear_srgb_to_oklab(r,g,b)
{
    let l = 0.4122214708 * r + 0.5363325363 * g + 0.0514459929 * b;
	let m = 0.2119034982 * r + 0.6806995451 * g + 0.1073969566 * b;
	let s = 0.0883024619 * r + 0.2817188376 * g + 0.6299787005 * b;

    let l_ = Math.cbrt(l);
    let m_ = Math.cbrt(m);
    let s_ = Math.cbrt(s);

    return [
        0.2104542553*l_ + 0.7936177850*m_ - 0.0040720468*s_,
        1.9779984951*l_ - 2.4285922050*m_ + 0.4505937099*s_,
        0.0259040371*l_ + 0.7827717662*m_ - 0.8086757660*s_,
    ];
}

function oklab_to_linear_srgb(L,a,b)
{

    let l_ = L + 0.3963377774 * a + 0.2158037573 * b;
    let m_ = L - 0.1055613458 * a - 0.0638541728 * b;
    let s_ = L - 0.0894841775 * a - 1.2914855480 * b;

    let l = l_*l_*l_;
    let m = m_*m_*m_;
    let s = s_*s_*s_;

    return [
		(+4.0767416621 * l - 3.3077115913 * m + 0.2309699292 * s),
		(-1.2684380046 * l + 2.6097574011 * m - 0.3413193965 * s),
		(-0.0041960863 * l - 0.7034186147 * m + 1.7076147010 * s),
    ];
}

function toe(x)
{
    const k_1 = 0.206
    const k_2 = 0.03
    const k_3 = (1+k_1)/(1+k_2)

    return 0.5*(k_3*x - k_1 + Math.sqrt((k_3*x - k_1)*(k_3*x - k_1) + 4*k_2*k_3*x))
}

function toe_inv(x)
{
    const k_1 = 0.206
    const k_2 = 0.03
    const k_3 = (1+k_1)/(1+k_2)
    return (x*x + k_1*x)/(k_3*(x+k_2))
}

// Finds the maximum saturation possible for a given hue that fits in sRGB
// Saturation here is defined as S = C/L
// a and b must be normalized so a^2 + b^2 == 1
function compute_max_saturation(a, b)
{
    // Max saturation will be when one of r, g or b goes below zero.

    // Select different coefficients depending on which component goes below zero first
    let k0, k1, k2, k3, k4, wl, wm, ws;

    if (-1.88170328 * a - 0.80936493 * b > 1)
    {
        // Red component
        k0 = +1.19086277; k1 = +1.76576728; k2 = +0.59662641; k3 = +0.75515197; k4 = +0.56771245;
        wl = +4.0767416621; wm = -3.3077115913; ws = +0.2309699292;
    }
    else if (1.81444104 * a - 1.19445276 * b > 1)
    {
        // Green component
        k0 = +0.73956515; k1 = -0.45954404; k2 = +0.08285427; k3 = +0.12541070; k4 = +0.14503204;
        wl = -1.2684380046; wm = +2.6097574011; ws = -0.3413193965;
    }
    else
    {
        // Blue component
        k0 = +1.35733652; k1 = -0.00915799; k2 = -1.15130210; k3 = -0.50559606; k4 = +0.00692167;
        wl = -0.0041960863; wm = -0.7034186147; ws = +1.7076147010;
    }

    // Approximate max saturation using a polynomial:
    let S = k0 + k1 * a + k2 * b + k3 * a * a + k4 * a * b;

    // Do one step Halley's method to get closer
    // this gives an error less than 10e6, except for some blue hues where the dS/dh is close to infinite
    // this should be sufficient for most applications, otherwise do two/three steps

    let k_l = +0.3963377774 * a + 0.2158037573 * b;
    let k_m = -0.1055613458 * a - 0.0638541728 * b;
    let k_s = -0.0894841775 * a - 1.2914855480 * b;

    {
        let l_ = 1 + S * k_l;
        let m_ = 1 + S * k_m;
        let s_ = 1 + S * k_s;

        let l = l_ * l_ * l_;
        let m = m_ * m_ * m_;
        let s = s_ * s_ * s_;

        let l_dS = 3 * k_l * l_ * l_;
        let m_dS = 3 * k_m * m_ * m_;
        let s_dS = 3 * k_s * s_ * s_;

        let l_dS2 = 6 * k_l * k_l * l_;
        let m_dS2 = 6 * k_m * k_m * m_;
        let s_dS2 = 6 * k_s * k_s * s_;

        let f  = wl * l     + wm * m     + ws * s;
        let f1 = wl * l_dS  + wm * m_dS  + ws * s_dS;
        let f2 = wl * l_dS2 + wm * m_dS2 + ws * s_dS2;

        S = S - f * f1 / (f1*f1 - 0.5 * f * f2);
    }

    return S;
}

function find_cusp(a, b)
{
	// First, find the maximum saturation (saturation S = C/L)
	let S_cusp = compute_max_saturation(a, b);

	// Convert to linear sRGB to find the first point where at least one of r,g or b >= 1:
	let rgb_at_max = oklab_to_linear_srgb(1, S_cusp * a, S_cusp * b);
	let L_cusp = Math.cbrt(1 / Math.max(Math.max(rgb_at_max[0], rgb_at_max[1]), rgb_at_max[2]));
	let C_cusp = L_cusp * S_cusp;

	return [ L_cusp , C_cusp ];
}

// Finds intersection of the line defined by
// L = L0 * (1 - t) + t * L1;
// C = t * C1;
// a and b must be normalized so a^2 + b^2 == 1
function find_gamut_intersection(a, b, L1, C1, L0, cusp=null)
{
    if (!cusp)
    {
        // Find the cusp of the gamut triangle
        cusp = find_cusp(a, b);
    }

	// Find the intersection for upper and lower half seprately
	let t;
	if (((L1 - L0) * cusp[1] - (cusp[0] - L0) * C1) <= 0)
	{
		// Lower half

		t = cusp[1] * L0 / (C1 * cusp[0] + cusp[1] * (L0 - L1));
	}
	else
	{
		// Upper half

		// First intersect with triangle
		t = cusp[1] * (L0 - 1) / (C1 * (cusp[0] - 1) + cusp[1] * (L0 - L1));

		// Then one step Halley's method
		{
			let dL = L1 - L0;
			let dC = C1;

			let k_l = +0.3963377774 * a + 0.2158037573 * b;
			let k_m = -0.1055613458 * a - 0.0638541728 * b;
			let k_s = -0.0894841775 * a - 1.2914855480 * b;

			let l_dt = dL + dC * k_l;
			let m_dt = dL + dC * k_m;
			let s_dt = dL + dC * k_s;


			// If higher accuracy is required, 2 or 3 iterations of the following block can be used:
			{
				let L = L0 * (1 - t) + t * L1;
				let C = t * C1;

				let l_ = L + C * k_l;
				let m_ = L + C * k_m;
				let s_ = L + C * k_s;

				let l = l_ * l_ * l_;
				let m = m_ * m_ * m_;
				let s = s_ * s_ * s_;

				let ldt = 3 * l_dt * l_ * l_;
				let mdt = 3 * m_dt * m_ * m_;
				let sdt = 3 * s_dt * s_ * s_;

				let ldt2 = 6 * l_dt * l_dt * l_;
				let mdt2 = 6 * m_dt * m_dt * m_;
				let sdt2 = 6 * s_dt * s_dt * s_;

				let r = 4.0767416621 * l - 3.3077115913 * m + 0.2309699292 * s - 1;
				let r1 = 4.0767416621 * ldt - 3.3077115913 * mdt + 0.2309699292 * sdt;
				let r2 = 4.0767416621 * ldt2 - 3.3077115913 * mdt2 + 0.2309699292 * sdt2;

				let u_r = r1 / (r1 * r1 - 0.5 * r * r2);
				let t_r = -r * u_r;

				let g = -1.2684380046 * l + 2.6097574011 * m - 0.3413193965 * s - 1;
				let g1 = -1.2684380046 * ldt + 2.6097574011 * mdt - 0.3413193965 * sdt;
				let g2 = -1.2684380046 * ldt2 + 2.6097574011 * mdt2 - 0.3413193965 * sdt2;

				let u_g = g1 / (g1 * g1 - 0.5 * g * g2);
				let t_g = -g * u_g;

				let b = -0.0041960863 * l - 0.7034186147 * m + 1.7076147010 * s - 1;
				let b1 = -0.0041960863 * ldt - 0.7034186147 * mdt + 1.7076147010 * sdt;
				let b2 = -0.0041960863 * ldt2 - 0.7034186147 * mdt2 + 1.7076147010  * sdt2;

				let u_b = b1 / (b1 * b1 - 0.5 * b * b2);
				let t_b = -b * u_b;

				t_r = u_r >= 0 ? t_r : 10e5;
				t_g = u_g >= 0 ? t_g : 10e5;
				t_b = u_b >= 0 ? t_b : 10e5;

				t += Math.min(t_r, Math.min(t_g, t_b));
			}
		}
	}

	return t;
}

function get_ST_max(a_,b_, cusp=null)
{
    if (!cusp)
    {
        cusp = find_cusp(a_, b_);
    }

    let L = cusp[0];
    let C = cusp[1];
    return [C/L, C/(1-L)];
}

function get_ST_mid(a_,b_)
{
    S = 0.11516993 + 1/(
        + 7.44778970 + 4.15901240*b_
        + a_*(- 2.19557347 + 1.75198401*b_
        + a_*(- 2.13704948 -10.02301043*b_
        + a_*(- 4.24894561 + 5.38770819*b_ + 4.69891013*a_
        )))
    );

    T = 0.11239642 + 1/(
        + 1.61320320 - 0.68124379*b_
        + a_*(+ 0.40370612 + 0.90148123*b_
        + a_*(- 0.27087943 + 0.61223990*b_
        + a_*(+ 0.00299215 - 0.45399568*b_ - 0.14661872*a_
        )))
    );

    return [S, T];
}

function get_Cs(L, a_, b_)
{
    cusp = find_cusp(a_, b_);

    let C_max = find_gamut_intersection(a_,b_,L,1,L,cusp);
    let ST_max = get_ST_max(a_, b_, cusp);

    let S_mid = 0.11516993 + 1/(
        + 7.44778970 + 4.15901240*b_
        + a_*(- 2.19557347 + 1.75198401*b_
        + a_*(- 2.13704948 -10.02301043*b_
        + a_*(- 4.24894561 + 5.38770819*b_ + 4.69891013*a_
        )))
    );

    let T_mid = 0.11239642 + 1/(
        + 1.61320320 - 0.68124379*b_
        + a_*(+ 0.40370612 + 0.90148123*b_
        + a_*(- 0.27087943 + 0.61223990*b_
        + a_*(+ 0.00299215 - 0.45399568*b_ - 0.14661872*a_
        )))
    );

    let k = C_max/Math.min((L*ST_max[0]), (1-L)*ST_max[1]);

    let C_mid;
    {
        let C_a = L*S_mid;
        let C_b = (1-L)*T_mid;

        C_mid = 0.9*k*Math.sqrt(Math.sqrt(1/(1/(C_a*C_a*C_a*C_a) + 1/(C_b*C_b*C_b*C_b))));
    }

    let C_0;
    {
        let C_a = L*0.4;
        let C_b = (1-L)*0.8;

        C_0 = Math.sqrt(1/(1/(C_a*C_a) + 1/(C_b*C_b)));
    }

    return [C_0, C_mid, C_max];
}

function okhsl_to_srgb(h,s,l)
{
    if (l == 1)
    {
        return [255,255,255];
    }

    else if (l == 0)
    {
        return [0,0,0];
    }

    let a_ = Math.cos(2*Math.PI*h);
    let b_ = Math.sin(2*Math.PI*h);
    let L = toe_inv(l);

    let Cs = get_Cs(L, a_, b_);
    let C_0 = Cs[0];
    let C_mid = Cs[1];
    let C_max = Cs[2];

    let C, t, k_0, k_1, k_2;
    if (s < 0.8)
    {
        t = 1.25*s;
        k_0 = 0;
        k_1 = 0.8*C_0;
        k_2 = (1-k_1/C_mid);
    }
    else
    {
        t = 5*(s-0.8);
        k_0 = C_mid;
        k_1 = 0.2*C_mid*C_mid*1.25*1.25/C_0;
        k_2 = (1 - (k_1)/(C_max - C_mid));
    }

    C = k_0 + t*k_1/(1-k_2*t);

    // If we would only use one of the Cs:
    //C = s*C_0;
    //C = s*1.25*C_mid;
    //C = s*C_max;

    let rgb = oklab_to_linear_srgb(L, C*a_, C*b_);
    return [
        255*srgb_transfer_function(rgb[0]),
        255*srgb_transfer_function(rgb[1]),
        255*srgb_transfer_function(rgb[2]),
    ]
}

function srgb_to_okhsl(r,g,b)
{
    let lab = linear_srgb_to_oklab(
        srgb_transfer_function_inv(r/255),
        srgb_transfer_function_inv(g/255),
        srgb_transfer_function_inv(b/255)
    );

    let C = Math.sqrt(lab[1]*lab[1] +lab[2]*lab[2]);
    let a_ = lab[1]/C;
    let b_ = lab[2]/C;

    let L = lab[0];
    let h = 0.5 + 0.5*Math.atan2(-lab[2], -lab[1])/Math.PI;

    let Cs = get_Cs(L, a_, b_)
    let C_0 = Cs[0];
    let C_mid = Cs[1];
    let C_max = Cs[2];

    let s;
    if (C < C_mid)
    {
        let k_0 = 0;
        let k_1 = 0.8*C_0;
        let k_2 = (1-k_1/C_mid);

        let t = (C - k_0)/(k_1 + k_2*(C - k_0));
        s = t*0.8;
    }
    else
    {
        let k_0 = C_mid;
        let k_1 = 0.2*C_mid*C_mid*1.25*1.25/C_0;
        let k_2 = (1 - (k_1)/(C_max - C_mid));

        let t = (C - k_0)/(k_1 + k_2*(C - k_0));
        s = 0.8 + 0.2*t;
    }

    let l = toe(L);
    return [h,s,l];
}


function okhsv_to_srgb(h,s,v)
{
    let a_ = Math.cos(2*Math.PI*h);
    let b_ = Math.sin(2*Math.PI*h);

    let ST_max = get_ST_max(a_,b_);
    let S_max = ST_max[0];
    let S_0 = 0.5;
    let T  = ST_max[1];
    let k = 1 - S_0/S_max;

    let L_v = 1 - s*S_0/(S_0+T - T*k*s)
    let C_v = s*T*S_0/(S_0+T-T*k*s)

    let L = v*L_v;
    let C = v*C_v;

    // to present steps along the way
    //L = v;
    //C = v*s*S_max;
    //L = v*(1 - s*S_max/(S_max+T));
    //C = v*s*S_max*T/(S_max+T);

    let L_vt = toe_inv(L_v);
    let C_vt = C_v * L_vt/L_v;

    let L_new =  toe_inv(L); // * L_v/L_vt;
    C = C * L_new/L;
    L = L_new;

    let rgb_scale = oklab_to_linear_srgb(L_vt,a_*C_vt,b_*C_vt);
    let scale_L = Math.cbrt(1/(Math.max(rgb_scale[0],rgb_scale[1],rgb_scale[2],0)));

    // remove to see effect without rescaling
    L = L*scale_L;
    C = C*scale_L;

    let rgb = oklab_to_linear_srgb(L, C*a_, C*b_);
    return [
        255*srgb_transfer_function(rgb[0]),
        255*srgb_transfer_function(rgb[1]),
        255*srgb_transfer_function(rgb[2]),
    ]
}

function srgb_to_okhsv(r,g,b)
{
    let lab = linear_srgb_to_oklab(
        srgb_transfer_function_inv(r/255),
        srgb_transfer_function_inv(g/255),
        srgb_transfer_function_inv(b/255)
    );

    let C = Math.sqrt(lab[1]*lab[1] +lab[2]*lab[2]);
    let a_ = lab[1]/C;
    let b_ = lab[2]/C;

    let L = lab[0];
    let h = 0.5 + 0.5*Math.atan2(-lab[2], -lab[1])/Math.PI;

    let ST_max = get_ST_max(a_,b_);
    let S_max = ST_max[0];
    let S_0 = 0.5;
    let T = ST_max[1];
    let k = 1 - S_0/S_max;

    t = T/(C+L*T);
    let L_v = t*L;
    let C_v = t*C;

    L_vt = toe_inv(L_v);
    C_vt = C_v * L_vt/L_v;

    rgb_scale = oklab_to_linear_srgb(L_vt,a_*C_vt,b_*C_vt);
    scale_L = Math.cbrt(1/(Math.max(rgb_scale[0],rgb_scale[1],rgb_scale[2],0)));

    L = L/scale_L;
    C = C/scale_L;

    C = C * toe(L)/L;
    L = toe(L);

    v = L/L_v;
    s = (S_0+T)*C_v/((T*S_0) + T*k*C_v)

    return [h,s,v];
}

function hex_to_rgb(hex)
{
    if (hex.substr(0,1) == "#")
        hex = hex.substr(1);

    if (hex.match(/^([0-9a-f]{3})$/i))
    {
        let r = parseInt(hex.charAt(0),16)/15 * 255;
        let g = parseInt(hex.charAt(1),16)/15 * 255;
        let b = parseInt(hex.charAt(2),16)/15 * 255;
        return [r,g,b];
    }
    if (hex.match(/^([0-9a-f]{6})$/i))
    {
        let r = parseInt(hex.substr(0,2),16);
        let g = parseInt(hex.substr(2,2),16);
        let b = parseInt(hex.substr(4,2),16);
        return [r,g,b];
    }
    if (hex.match(/^([0-9a-f]{1})$/i))
    {
        let a = parseInt(hex.substr(0),16)/15 * 255;
        return [a,a,a];
    }
    if (hex.match(/^([0-9a-f]{2})$/i))
    {
        let a = parseInt(hex.substr(0,2),16);
        return [a,a,a];
    }

    return null;
}

function rgb_to_hex(r,g,b)
{
    function componentToHex(x)
    {
        var hex = Math.round(x).toString(16);
        return hex.length == 1 ? "0" + hex : hex;
    }

    return "#" + componentToHex(r) + componentToHex(g) + componentToHex(b);
}
</script>

<script>
// Line break added to fix vim breaking syntax highlighting on long lines
var named_colors =[["aliceblue","240,248,255"],["antiquewhite","250,235,215"],["aqua","0,255,255"],["aquamarine","127,255,212"],["azure","240,255,255"],["beige","245,245,220"],["bisque","255,228,196"],["black","0,0,0"],["blanchedalmond","255,235,205"],["blue","0,0,255"],["blueviolet","138,43,226"],["brown","165,42,42"],["burlywood","222,184,135"],["cadetblue","95,158,160"],["chartreuse","127,255,0"],["chocolate","210,105,30"],["coral","255,127,80"],["cornflowerblue","100,149,237"],["cornsilk","255,248,220"],["crimson","220,20,60"],["cyan","0,255,255"],["darkblue","0,0,139"],["darkcyan","0,139,139"],["darkgoldenrod","184,134,11"],["darkgray","169,169,169"],["darkgreen","0,100,0"],["darkgrey","169,169,169"],["darkkhaki","189,183,107"],["darkmagenta","139,0,139"],["darkolivegreen","85,107,47"],["darkorange","255,140,0"],["darkorchid","153,50,204"],["darkred","139,0,0"],["darksalmon","233,150,122"],["darkseagreen","143,188,143"],["darkslateblue","72,61,139"],["darkslategray","47,79,79"],["darkslategrey","47,79,79"],["darkturquoise","0,206,209"],["darkviolet","148,0,211"],["deeppink","255,20,147"],["deepskyblue","0,191,255"],["dimgray","105,105,105"],["dimgrey","105,105,105"],["dodgerblue","30,144,255"],["firebrick","178,34,34"],["floralwhite","255,250,240"],["forestgreen","34,139,34"],["fuchsia","255,0,255"],["gainsboro","220,220,220"],["ghostwhite","248,248,255"],["gold","255,215,0"],["goldenrod","218,165,32"],["gray","128,128,128"],["green","0,128,0"],["greenyellow","173,255,47"],["grey","128,128,128"],["honeydew","240,255,240"],["hotpink","255,105,180"],["indianred","205,92,92"],["indigo","75,0,130"],["ivory","255,255,240"],["khaki","240,230,140"],["lavender","230,230,250"],["lavenderblush","255,240,245"],["lawngreen","124,252,0"],["lemonchiffon","255,250,205"],["lightblue","173,216,230"],["lightcoral","240,128,128"],["lightcyan","224,255,255"],["lightgoldenrodyellow","250,250,210"],["lightgray","211,211,211"],["lightgreen","144,238,144"],["lightgrey","211,211,211"],["lightpink","255,182,193"],["lightsalmon","255,160,122"],["lightseagreen","32,178,170"],["lightskyblue","135,206,250"],["lightslategray","119,136,153"],["lightslategrey","119,136,153"],["lightsteelblue","176,196,222"],["lightyellow","255,255,224"],["lime","0,255,0"],["limegreen","50,205,50"],["linen","250,240,230"],["magenta","255,0,255"],["maroon","128,0,0"],["mediumaquamarine","102,205,170"],["mediumblue","0,0,205"],["mediumorchid","186,85,211"],["mediumpurple","147,112,219"],["mediumseagreen","60,179,113"],["mediumslateblue","123,104,238"],["mediumspringgreen","0,250,154"],["mediumturquoise","72,209,204"],["mediumvioletred","199,21,133"],["midnightblue","25,25,112"],["mintcream","245,255,250"],["mistyrose","255,228,225"],["moccasin","255,228,181"],["navajowhite","255,222,173"],["navy","0,0,128"],["oldlace","253,245,230"],["olive","128,128,0"],["olivedrab","107,142,35"],["orange","255,165,0"],["orangered","255,69,0"],["orchid","218,112,214"],["palegoldenrod","238,232,170"],
["palegreen","152,251,152"],["paleturquoise","175,238,238"],["palevioletred","219,112,147"],["papayawhip","255,239,213"],["peachpuff","255,218,185"],["peru","205,133,63"],["pink","255,192,203"],["plum","221,160,221"],["powderblue","176,224,230"],["purple","128,0,128"],["red","255,0,0"],["rosybrown","188,143,143"],["royalblue","65,105,225"],["saddlebrown","139,69,19"],["salmon","250,128,114"],["sandybrown","244,164,96"],["seagreen","46,139,87"],["seashell","255,245,238"],["sienna","160,82,45"],["silver","192,192,192"],["skyblue","135,206,235"],["slateblue","106,90,205"],["slategray","112,128,144"],["slategrey","112,128,144"],["snow","255,250,250"],["springgreen","0,255,127"],["steelblue","70,130,180"],["tan","210,180,140"],["teal","0,128,128"],["thistle","216,191,216"],["tomato","255,99,71"],["turquoise","64,224,208"],["violet","238,130,238"],["wheat","245,222,179"],["white","255,255,255"],["whitesmoke","245,245,245"],["yellow","255,255,0"],["yellowgreen","154,205,50"],["rebeccapurple","102,51,153"]]

function srgb_to_oklab(r,g,b) {
	return linear_srgb_to_oklab(
		srgb_transfer_function_inv(r/255),
		srgb_transfer_function_inv(g/255),
		srgb_transfer_function_inv(b/255)
	)
}

function oklab_to_srgb(L,a,b) {
	return oklab_to_linear_srgb(L,a,b).map(x=>srgb_transfer_function(x))
}

// From https://bottosson.github.io/posts/oklab/
function linear_srgb_to_oklab(r,g,b)
{
    var l = 0.4122214708 * r + 0.5363325363 * g + 0.0514459929 * b;
	var m = 0.2119034982 * r + 0.6806995451 * g + 0.1073969566 * b;
	var s = 0.0883024619 * r + 0.2817188376 * g + 0.6299787005 * b;

    var l_ = Math.cbrt(l);
    var m_ = Math.cbrt(m);
    var s_ = Math.cbrt(s);

    return [
        0.2104542553*l_ + 0.7936177850*m_ - 0.0040720468*s_,
        1.9779984951*l_ - 2.4285922050*m_ + 0.4505937099*s_,
        0.0259040371*l_ + 0.7827717662*m_ - 0.8086757660*s_,
    ];
}

function  oklab_to_linear_srgb(L,a,b)
{
    var l_ = L + 0.3963377774 * a + 0.2158037573 * b;
    var m_ = L - 0.1055613458 * a - 0.0638541728 * b;
    var s_ = L - 0.0894841775 * a - 1.2914855480 * b;

    var l = l_*l_*l_;
    var m = m_*m_*m_;
    var s = s_*s_*s_;

    return [
		+4.0767416621 * l - 3.3077115913 * m + 0.2309699292 * s,
		-1.2684380046 * l + 2.6097574011 * m - 0.3413193965 * s,
		-0.0041960863 * l - 0.7034186147 * m + 1.7076147010 * s,
    ];
}

// Finally:
dynamic_color_table.innerHTML = named_colors.map(x=>
	[
		x[0],
		x[1].split(',').map(y=>y*1),
		srgb_to_oklab(...x[1].split(',').map(y=>y*1))[0],
		rgb_to_hsl(...x[1].split(',').map(y=>y*1))[1]
	]
).sort((a,b)=>a[2]-b[2]).map(x=>'<tr><td style="background: ' + x[0] + ';"></td><td>' + x[0] + '</td><td>' + x[2].toFixed(4) + '</td><!--<td>' + x[3].toFixed(4) + '</td>--></tr>').join('')
</script>
