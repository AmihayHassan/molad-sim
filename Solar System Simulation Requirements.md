# 3D Solar System & Astronomical Simulation - Requirements Specification

**Document Version:** 3.0  
**Target Environment:** Modern Web Browsers (WebGL 2.0 / Three.js r128)  
**Primary Focus:** Orbital Mechanics, Axial Tilt Day/Night Length Variations, Lunar Phases, and Clean Interactive Spaces.

---

## 1. Executive Summary
This document outlines the software requirements and technical architecture for a high-fidelity, standalone 3D interactive simulation of the Sun, Earth, and Moon system (with optional expansion to outer planets). The simulation provides real-time visualizations of astronomical phenomena, specifically addressing:
1. **Seasonal Day/Night Length Variations** caused by Earth's $23.44^{\circ}$ axial tilt.
2. **Lunar Phases** resulting from the relative geometry of the Sun-Earth-Moon system.
3. **Clean Space Visuals** adhering to realistic astrophysical representation without arbitrary clutter.
4. **Interactive Controls** including speed scaling, orbit toggles, camera perspective modes, and outer planet visibility.

---

## 2. Core Functional Requirements

### 2.1 The Sun (Central Body)
* **Position:** Fixed permanently at coordinate origin $(0, 0, 0)$.
* **Visuals:** Emissive glowing core combined with additive blending sprite shaders to simulate solar corona and gas activity.
* **Lighting:** Acts as the primary astrophysical light source (`PointLight`) casting dynamic shadows across the solar system.

### 2.2 Earth & Orbital Mechanics
* **Axial Tilt:** Fixed at **$23.44^{\circ}$** relative to the ecliptic plane, maintaining a constant spatial orientation pointing toward celestial north.
* **Surface Textures:** Procedurally generated textures featuring landmasses, topography (bump mapping), ocean specular highlights, dynamic independent cloud layer rotation, and Rayleigh scattering atmospheric glow.
* **Daylight Calculation Engine:** Real-time computation of daylight hours based on latitude $\phi$ and solar declination $\delta$:
  $$\cos(\omega_0) = -\tan(\phi) \cdot \tan(\delta)$$
  $$\text{Daylight Hours} = \frac{2}{15^{\circ}} \cdot \arccos\big(-\tan(\phi) \cdot \tan(\delta)\big)$$

### 2.3 Moon & Lunar Phases
* **Orbit:** Revolves around Earth in a 29.53-day synodic month cycle with orbital inclination ($5.14^{\circ}$).
* **Tidal Locking:** Synchronous rotation ensuring the Moon always faces Earth with consistent crater topography (displacement/normal maps).
* **Phase HUD:** Integrated 2D real-time preview canvas demonstrating the exact illuminated percentage and phase nomenclature.

### 2.4 Simulation Controls & Toggles
* **Time & Speed Control:** Continuous slider and quick-preset buttons ($0.2\times$, $1\times$, $3\times$, $8\times$) governing simulation step increments.
* **Orbit Lines Toggle:** Checkbox to show/hide elliptical orbit paths for Earth, Moon, and planetary bodies.
* **Outer Planets Toggle:** Optional inclusion of Mercury, Venus, Mars, Jupiter, Saturn (with rings), Uranus, and Neptune in clean heliocentric arrangement.

---

## 3. Technical Specifications & Mathematical Models

| Parameter | Symbol | Default Value | Description |
| :--- | :---: | :---: | :--- |
| Earth Orbit Radius | $R_E$ | 58 units | Distance from Sun to Earth |
| Moon Orbit Radius | $R_M$ | 8.5 units | Distance from Earth to Moon |
| Earth Axial Tilt | $\varepsilon$ | $23.44^{\circ}$ | Obliquity of the ecliptic |
| Synodic Month | $T_m$ | 29.53 days | Full lunar phase cycle duration |

---

## 4. Code Architecture & Implementation Snippet

The application is encapsulated within a single portable HTML file utilizing Three.js:

```javascript
// Core animation loop example
function animate() {
    requestAnimationFrame(animate);
    if (state.isPlaying) {
        earthMesh.rotation.y += 0.012 * state.simSpeed;
        cloudsMesh.rotation.y += 0.0145 * state.simSpeed;
        state.lunarDay = (state.lunarDay + 0.015 * state.simSpeed) % 29.53;
        state.dayOfYear = ((state.dayOfYear - 1 + 0.015 * state.simSpeed) % 365) + 1;
        updateAstronomyAndUI();
    }
    controls.update();
    renderer.render(scene, camera);
}
```

---
*End of Requirements Specification.*