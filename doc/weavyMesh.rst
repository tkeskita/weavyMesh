WeavyMesh
=========

*WeavyMesh* is currently an idea for an automatic 3D volume mesh
generation procedure. The main idea is that in the meshing procedure,
a 3D surface mesh is "weaved" or "stitched" into a 3D cubic octree
volume mesh. The procedure should create a fairly orthogonal boundary
layer in the mesh, which is expected to be hex-dominant.

Algorithm Description
---------------------

1. Background mesh generation is done by a cubic octree
decomposition. Refinement of background cells is based on the
proximity to the surface mesh, or edge intersections with the octree
mesh.

.. image:: images/weavy_algorithm_01.png

.. image:: images/weavy_algorithm_02.png

2. The cells in the background mesh which cut the surface mesh are
removed to create space for the weaved mesh. The boundary faces,
boundary edges and boundary points (e.g. white dot in the picture
below) remaining in the background mesh after the removal act as
"secondary side elements" in the weaving process, while the elements
of the surface mesh acts as "primary side elements".

.. image:: images/weavy_algorithm_03.png

3. Surface mesh is decimated to ensure that the weaving stage will
create manifold cells. Ray tracing from primary surface mesh face
centres towards face normal direction (or from primary points towards
point normals?) is made to evaluate "weavability" of each surface face
(or point). If traced ray hits second side faces (possibly with a
check for alignment with secondary face normal direction?), the
weaving is deemed possible. Primary side faces which can't be weaved
(ray hits a primary face) are "pushed outwards" (point extrusion?) in
a way which creates meshless voids (this might not be a trivial
task?). Additional collapsing can be made if distance to closest
second side point, or secondary face normal direction vs. closest
point direction, is "too large"?

.. image:: images/weavy_algorithm_04.png

4. The main weaving stage. Each **face** on both the primary side and
secondary side are connected to **the closest opposite weaved point**
on the other side. This will create pyramid cells, if only quad faces
are applied on the surface mesh. After weaving is done, there should
be no voids left between the primary and the secondary elements. Not
sure if there might be some issues to overcome to make this process
always complete in a manifold mesh?

.. image:: images/weavy_algorithm_05.png

5. Subdivision of cells next to boundary surfaces. Cells are split in
the normal-to-surface direction. This is repeated a second time.

.. image:: images/weavy_algorithm_06.png

.. image:: images/weavy_algorithm_07.png

6. Collapsing of first layer small top faces. The small "top" faces
(assuming boundary faces are "bottom" faces on the boundary cell
layer) on the boundary cell layer are collapsed away, except on sharp
feature edges. This will ensure most of boundary layer cells are
prismatic. Only the top faces near very sharp feature edges (>60 or
>90 deg?) need to be left uncollapsed.

.. image:: images/weavy_algorithm_08.png

7. The subdivision of the boundary cell layer can be repeated to
create more boundary layers. Finally (or after earlier stages), the
mesh is smoothened e.g. by constrained centroidal smoothing (see e.g.
`smoothMesh <https://github.com/tkeskita/smoothMesh>`_.

.. image:: images/weavy_algorithm_09.png
