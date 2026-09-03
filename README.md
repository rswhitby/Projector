# The direction of the projectors

An interactive demonstration of parallel projection, built for ARCH 5511 *Constructed Drawing I*, Department of Architecture, Cornell AAP.

Two linked panels. The left shows the apparatus: an object, a bounding box, a picture plane, and the projector lines running from the object to that plane. The right shows the drawing those projectors produce. Changing the direction of the projectors — or the tilt of the plane relative to them — is the only variable, and the taxonomy of projection types falls out of it.

Single HTML file, no build step, no dependencies. All projection, hidden-surface, and silhouette geometry is computed from scratch in about 700 lines of vanilla JavaScript on a 2D canvas.

## Running it

Open `index.html` in a browser. That's the whole thing.

To serve it locally:

```
python3 -m http.server 8000
```

then visit `http://localhost:8000`.

## Publishing to GitHub Pages

1. Push this repository to GitHub.
2. Settings → Pages → Source: **Deploy from a branch**, branch `main`, folder `/ (root)`.
3. The app appears at `https://<user>.github.io/<repo>/` within a minute or two.

`index.html` is at the repository root, so no build or workflow is needed.

## What it demonstrates

**Multiview.** The glass box: three planes, each with its own set of projectors normal to it. The unfold slider swings the top and side planes into the plane of the front view, which is the sheet layout. Third angle. The result panel draws the layout with the 45° miter line and the transfer lines between views.

**Axonometric.** Projectors stay parallel and stay normal to the plane; only the plane turns. The readout reports the foreshortening ratio and page angle of each principal axis and classifies the result as isometric, dimetric, or trimetric. The categories are consequences of the direction of view, not definitions imposed on it.

**Oblique.** Projectors remain parallel but are no longer normal to the plane. One face is held true at full scale. Front-true gives cavalier and cabinet; plan-true gives the military or planometric projection, with plan rotation and height ratio as separate controls.

**Perspective.** Included as a contrast rather than a member of the family. The projectors visibly converge on a station point; increasing the distance flattens the pencil toward parallel.

The lineage line under the result panel always reads the full path — *parallel → orthographic → axonometric → isometric* — which keeps axonometric inside the orthographic branch rather than beside it.

## Objects

Twelve solids. Four are prismatic: cube, L-block, step, tee. Eight involve curvature: cylinder, tube, plate with a through hole, link, filleted bracket, knob, torus, flange.

The curved solids make a point the prismatic ones cannot. A curved surface carries no edges. What reads as its outline is a silhouette, and the silhouette is a property of the drawing rather than of the object — turn the projectors and it slides across the surface. In multiview the silhouette is recomputed independently for each of the three planes, so the outline of the same feature lands in a different place in each view. Circles read as circles only on a plane they are parallel to: put the plate in cabinet oblique with the front face true, then switch to isometric, and the hole becomes an ellipse.

## How it works

Geometry is built by two generators. `extrude` sweeps a set of 2D loops along a principal axis, with the first loop as the outer boundary and the rest as holes; `revolve` sweeps a profile about the z-axis, with support for poles and closed profiles. A `fillet` helper rounds polygon corners and marks the resulting vertices as tangent-continuous.

Faces are stored as sets of loops so that a face can carry holes, and are filled even-odd. Face orientation is made globally consistent by computing the signed volume of the mesh and flipping the normals if it comes out negative.

Every edge is tagged hard or smooth. Hard edges are always drawn. Smooth edges are tessellation seams on a curved surface and are drawn only where the two adjacent faces disagree about facing the projectors — a silhouette test evaluated per frame, per projection direction. Hidden-line removal is a painter's algorithm: faces sorted back to front along the projector direction, filled opaque, then stroked.

A single `proj(P, spec)` function handles every projection in the app. A spec carries a plane origin, normal, and two in-plane basis vectors, plus either a fixed projector direction (parallel) or a station point (perspective). Everything else — multiview, axonometric, cavalier, cabinet, military, perspective — is a different spec passed to the same function.

## Files

```
index.html    the entire application
README.md     this file
LICENSE       MIT
```

## License

MIT. See `LICENSE`. Course materials that use it remain the author's.
