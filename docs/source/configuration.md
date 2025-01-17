# Configuration

This chapter describes the `pyCoilGen` confiuration parameters. 

> **Note:** All values are specified in SI units, i.e. metre, Ampere, Tesla.

## Basic Settings

- `output_directory` (Type: ``str``, Default: `Current  working directory`)

  The output directory where intermediate images and the final output will be written to.

- `project_name` (Type: ``str``, Default: `'CoilGen'`)

  A project name. The project name is used to create output files.

- `persistence_dir` (Type: ``str``, Default: `'debug'`)

  The directory where project snapshots are written. A snapshot of the internal state is automatically written to this location when any unhandled exception occurs.

- `debug` (Type: `int`, Default: `0`)

  The debug verbosity level: 0 = None, 1 = Basic, 2 = Verbose.

  The debug verbosity level only has any effect if logging is configured to include the debugging level. See `parameter_where_log_level_is_set`.

  ```python
  if __name__ == "__main__":
      # Set up logging
      log = logging.getLogger(__name__)
      logging.basicConfig(level=logging.DEBUG)

      ...
  ```

- `geometry_source_path` (Type: `str`, Default: `Current Working Directory + '/Geometry_Data'`)

  The directory where `.stl` geometry files are located.

## Mesh Geometry

The coil mesh geometry must be specified. It can either be loaded from a pre-optimised NumPy pickle file or specified in parts.

### Mesh Files

- `coil_mesh_file` (Type: `str`, Default: `'none'`)

  The definition of the winding coil surface. 

  Either specify the filename of an `.stl` file to be loaded from `geometry_source_path`, or use one of the built-in mesh specifications. When using a built-in mesh specification, the mesh parameters must also be specified.

#### Built-in Meshes

The winding coil surface can be specified using a subset of built-in types. 

To use one of the built-in types, set `coil_mesh_file` to one of the following special names. The mesh parameters are then specified using a second parameter. 

- `create cylinder mesh`

  Create a cylindrical mesh according to `cylinder_mesh_parameter_list` (Type: `list of numeric`, Default: `[0.8, 0.3, 20, 20, 1, 0, 0, 0]`)

        cylinder_height (`float`): Height of the cylinder.
        cylinder_radius (`float`): Radius of the cylinder.
        num_circular_divisions (`int`): Number of circular divisions.
        num_longitudinal_divisions (`int`): Number of longitudinal divisions.
        rotation_vector_x (`float`): X-component of the rotation vector.
        rotation_vector_y (`float`): Y-component of the rotation vector.
        rotation_vector_z (`float`): Z-component of the rotation vector.
        rotation_angle (`float`): Rotation angle.


- `create planar mesh` 

  Create a planar mesh according to `planar_mesh_parameter_list` (Type: `list of numeric`, Default: `[0.25, 0.25, 20, 20, 1, 0, 0, 0, 0, 0, 0]`)

        planar_height (`float`): Height of the planar mesh.
        planar_width (`float`): Width of the planar mesh.
        num_lateral_divisions (`int`): Number of divisions in the lateral direction.
        num_longitudinal_divisions (`int`): Number of divisions in the longitudinal direction.
        rotation_vector_x (`float`): X component of the rotation vector.
        rotation_vector_y (`float`): Y component of the rotation vector.
        rotation_vector_z (`float`): Z component of the rotation vector.
        rotation_angle (`float`): Rotation angle in radians.
        center_position_x (`float`): X component of the center position.
        center_position_y (`float`): Y component of the center position.
        center_position_z (`float`): Z component of the center position.


- `create bi-planar mesh`

  Create a bi-planar mesh according to `biplanar_mesh_parameter_list` (Type: `list of numeric`, Default: `[0.25, 0.25, 20, 20, 1, 0, 0, 0, 0, 0, 0.2]`)

        planar_height (`float`): Height of the planar mesh.
        planar_width (`float`): Width of the planar mesh.
        num_lateral_divisions (`int`): Number of divisions in the lateral direction.
        num_longitudinal_divisions (`int`): Number of divisions in the longitudinal direction.
        target_normal_x (`float`): X-component of the target normal vector.
        target_normal_y (`float`): Y-component of the target normal vector.
        target_normal_z (`float`): Z-component of the target normal vector.
        center_position_x (`float`): X-coordinate of the center position.
        center_position_y (`float`): Y-coordinate of the center position.
        center_position_z (`float`): Z-coordinate of the center position.
        plane_distance (`float`): Distance between the two planes.


<!-- Unused parameters
`double_cone_mesh_parameter_list` (Type: `list of float`, Default: `[0.8, 0.3, 0.3, 0.1, 20, 20, 1, 0, 0, 0]`)
  Parameters for the generation of a double cone ('diabolo') shaped mesh.

`circular_mesh_parameter_list` (Type: `list of float`, Default: `[0.25, 20, 1, 0, 0, 0, 0, 0, 0]`)
  Parameters for the generation of the (default) circular mesh.
-->

### Subdividing the Mesh

Once the mesh has been loaded, the mesh resolution can be increased using subdivision.

- `iteration_num_mesh_refinement` (Type: `int`, Default: `0`)

  The number of refinement iterations of the mesh. At each iteration, every mesh face is subdivided into four faces.


## Parameterise Mesh

The 3D coil winding surface needs to be projected onto a 2D plane in order to perform further processing.

- `surface_is_cylinder_flag` (Type: `bool`, Default: `True`)

  Provide a hint to `pyCoilGen` that the 3D coil can be projected onto 2D using a simple cylindrical projection.

If the cylindrical projection is inappropriate then an iterative mesh parameterisation approach is used.

- `circular_diameter_factor` (Type: `float`, Default: `1`)

  Circular diameter factor for projecting the 3D coil mesh to 2D.

## Target Field

The purpose of `pyCoilGen` is to generate coils that produce a desired target field. This target field could be a gradient field or a generic target field.

The target field can be either defined by a volume generated by a mesh loaded from an `.stl` file, as a sphere of a defined radius or loaded from a NumPy pickle file.

A gradient field is specified by using a field shape function.

### Specifying the Target Field Co-ordinates Using an `.stl` Mesh

The mesh defines the boundary of the target field and these parameters fine-tune the target field point selection.

- `target_mesh_file` (Type: `str`, Default: `'none'`)

  The mesh used to define the target field. 

If no file is specified, a spherical geometry is generated as a target volume, centred at the co-ordinates origin.

- `secondary_target_mesh_file` (Type: `str`, Default: `'none'`)

  File of the secondary target mesh, for example for suppressed outer regions in active shields.

- `secondary_target_weight` (Type: `float`, Default: `1`)

  Weight for the secondary target points.

- `use_only_target_mesh_verts` (Type: `bool`, Default: `False`)

  If True, indicates that only the vertices of the mesh are to be used. By default the target volume is populated with points. 

- `target_region_resolution` (Type: `int`, Default: `10`)

  Defines how many target points to create per dimension within the target region.

  Only used if `use_only_target_mesh_verts` is `False`.

### Specifying the Target Field Co-ordinates Using a Sphere
When both `target_field_definition_file` and `target_field_definition_file` are `'none'` then the target field co-ordinates are specified using a spherical volume.

- `target_region_radius` (Type: `float`, Default: `0.15`)

  The radius of the spherical target field. 

  The target field co-ordinates are then created by sub-dividing the radius using  `target_region_resolution`, which defines how many co-ordinates to create along each axis.

- `set_roi_into_mesh_center` (Type: `bool`, Default: `False`)

  This flag is used to set the ROI into the geometric center of the mesh. 

  If set, the centre of the target sphere is moved to the mean of the target field vertices.


### Specifying the Gradient Field Shape Function

Once the target field co-ordinates have been specified, then gradient field vectors can be calculated.

- `field_shape_function` (Type: `str`, Default: `'x'`)

  The spatial function that defines the analytical function of the `z` component of the vector field. 
  For example, `x` means a linear gradient in the `x`-direction of the `z` component.

- `target_gradient_strength` (Type: `float`, Default: `1`)

  The gradient field strength in mT/m/A.

### Using a NumPy Pickle file
  
- `target_field_definition_file` (Type: `str`, Default: `'none'`)

  The name of the NumPy pickle file that contains the target field co-ordinates and field value. 

  The target field definition file allows users to specify non-analytical fields.

  If used, the target field file is loaded from the `target_fields` directory.

- `target_field_definition_field_name` (Type: `str`, Default: `'none'`)

  The field name of the target field definition within the NumPy pickle file.

#### Target Field File Structure

The target field NumPy pickle file consists of a single array containing a dictionary with at least two key-value
pairs: `coords` and another key-value pair.

The `coords` key-value pair must be a 3 by M array which specifies the of x,y and z-coordinates of each target field
point.

The other key-value pair, which provides the magnetic field component, is specified with the `target_field_definition_field_name`
property. The value may either by a single array of the same length (M) as `coords`, or a 3 by M array.

If the value is a 1-D array, then it is interpreted to be the z-component of the target field and will be used to
construct the required 3-D array by setting the x- and y-components to zero.

The following code snippet shows how to create the file from existing data.

```python
def save_bfield_file(filename: str, coords: np.ndarray, vector_field: np.ndarray):
    data = {
      'coords': coords,       # Assuming that coords is a (3,m) array of float.
      'b_field': vector_field # Assuming that vector_field is either an (m,) or (3,m) array of float.
      } 
    np.save(f'target_fields/{filename}.npy', [data], allow_pickle=True)
```

The data would be loaded by using:
```python
  parameters = {
      ...
      'target_field_definition_file': f'{filename}.npy',  # Target field file name
      'target_field_definition_field_name': 'b_field',    # Target field key name
      ...
  }
  solution = pyCoilGen(log, parameters)
```
## Discretisation and Calculation of Field Variables

### Winding Coil Contribution and Target Field Sensitivity

The magnetic field contribution and the target field sensitivity is calculated at every corresponding co-ordinate.

- `gauss_order` (Type: `int`, Default: `2`)

  This parameter determines the number of Gauss integration points used in the winding magnetic field calculations.


### Winding Coil Resistance

The winding coil resistance affects the gradient magnetic field due to the winding coil.

- `specific_conductivity_conductor` (Type: `float`, Default: `0.018e-6`)

  The conductivity of the winding coil.

- `conductor_thickness` (Type: `float`, Default: `0.005`)

  The thickness of the sheet current density within the stream function representation.


### Stream Function

The stream function represents the relationship between the coil parts and the target field.

`pyCoilGen` performs an optimisation calculation of the stream function.

- `sf_opt_method` (Type: `str`, Default: `'tikhonov'`)

  The stream function optimization method.

- `tikhonov_reg_factor` (Type: `float`, Default: `1`)

  Tikhonov regularization factor for the stream function optimization, for weighting the coil's resistance, and hence the dissipated power.

- `minimize_method` (Type: `str`, Default: `'SLSQP'`)

  The minimisation method to use in the the NumPy `minimize` function. If `sf_opt_method` is not `'tikhonov'`, then  the NumPy `minimize` function is used. 

- `minimize_method_parameters` (Type: `str`, Default:`"{'tol': 1e-6}"`)

  Additional minimize method parameters.

- `minimize_method_options` (Type: `str`, Default: `"{'disp': True, 'maxiter' : 100}"`)

  Additional minimize method options, specific to the method.

Please refer to the [`scipy.optimize.minimize` API documentation](https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.minimize.html)
for more information on optimization-related parameters.

## Pre-calculated Mesh and Stream Function

The stream function optimisation is a time-consuming process and dependent only on the input coil surfaces, target volume and optimisation
parameters, above.

Users wishing to explore coil designs can save the optimised data once, then re-load it while changing the remaining
parameters, below.

### Save

The combined mesh and stream function can be persisted for subsequent re-use.

- `sf_dest_file` (Type: `str`, Default: `'none'`)

  The filename (without extension) where to write the optimised stream function and other data to storage. 

  The file will be written to the `Pre_Optimized_Solutions` directory unless the filename contains any path delimiters (`/` or `\`). If the filename contains path delimiters, then the path is used as-provided. It is the user's responsibility to ensure that the path already exists.

**NOTE:** If they are both specified, `sf_source_file` takes precedence over `sf_dest_file`.

### Load

A pre-existing mesh and optimised stream function solution can be loaded from persistence.

- `sf_source_file` (Type: `str`, Default: `'none'`)

  The filename (without extension) of the file of the already optimized stream function. 

  The file is loaded from the `Pre_Optimized_Solutions` directory unless the filename contains any path delimiters (`/` or `\`). The `pyCoilGen_Data` directory is automatically included, if installed. If the filename contains path delimiters, then the path is used as-provided. 


## Build Contour Lines

The optimised current density must be analysed to determine the candidate wire paths. This is done by computing the equipotential contours.

### Contour Parameters
- `levels` (Type: `int`, Default: `10`)

  The number of potential levels. This determines the number of coil windings.

- `level_set_method` (Type: `str`, Default: `'primary'`)

  The method for calculating the level sets. Can be one of 'primary', 'combined' or 'independent'.

  The contour levels are calculated from the stream function with contributions from the different coil meshes (if more than one), according to the level set method.

  - Use 'primary' to calculate the contour potentials from primary coil mesh only. 
  - Use 'combined' to calculate the potentials from the combined mesh. 
  - Use 'independent' to calculate the potentials for each coil mesh independently.

  The best method depends on `pyCoilGen`. Users can examine the final computed target field and computed errors to inform their decision.

- `pot_offset_factor` (Type: `float`, Default: `1/2`)

  The factor to control the contour level step, based on the stream function range.

- `smooth_factor` (Type: `int`, Default: `1`)

  The number of points along the contour to be used for smoothing. 

  Each point is replaced with the moving average of the specified number of neighbouring points. Smoothing only takes place when the `smooth_factor` is greater than 1.

- `min_loop_significance` (Type: `int`, Default: `1`)

  The minimal required field contribution (as a percent) to the target field. Contours that contribute less than this are deleted.


- `skip_calculation_min_winding_distance` (Type: `bool`, Default: `True`)

  A flag to skip calculation of minimum distance between calculated contour lines.

  `pyCoilGen` can calculate the PCB track width using the minimum width between contours if this flag is `False`.

## Contour Topology

Once the equipotential contours have been identified, they are processed and grouped topologically.

[Figure showing stream function discretization and contour generation](https://onlinelibrary.wiley.com/doi/10.1002/mrm.29294#mrm29294-fig-0003)


### Topology Parameters

[Figure showing details of contour interconnections](https://onlinelibrary.wiley.com/doi/10.1002/mrm.29294#mrm29294-fig-0004)

Neighbouring equipotential contours within a topological group are cut and joined with their neighbours.
In order to reduce distortions induced by the cuts, they are performed along the intersection of the plane oriented with the magnetic field and the coil surface.
Each contour thus has two cut points, one in the positive B0 direction and one against the B0 direction. 
These cut locations are termed "high" and "low" cuts, respectively.

### Interconnect Contours / Build Wire Path

Connect the groups and shift the return paths over the surface.

- `force_cut_selection` (Type: `list`, Default: `[]`)

  The direction of cuts that join neighbouring contours, to form a topological group. The allowed options are `'high'` or `'low'`. 

  The array must either contain a single entry, which is used for all cut points, or match the number of topological groups, which is displayed by `pyCoilGen` during processing.

- `b_0_direction` (Type: `float array`, Default: `[0, 0, 1]`)

  Direction (vector) along which the interconnections will be aligned.

- `interconnection_cut_width` (Type: `float`, Default: `0.01`)

  Width (in metres) of the cut used to connect neighbouring contours and to join contour groups to form a single wire path.

### Return Paths

These parameters affect the generation of the return paths (Figure 4(c)).

- `skip_normal_shift` (Type: `bool`, Default: `False`)

  If True, skips the shifting of return paths around the contour loop.

  Shifting the return paths helps to align segments so that multiple segments of a single return path can all be raised together.

- `normal_shift_length` (Type: `float`, Default: `0.001`)

  Distance in metres which intersecting wire paths will be separated along the normal direction of the surface.

- `normal_shift_smooth_factors` (Type: `list of 3 integers`, Default: `[2, 3, 2]`)

  Parameters used to smooth the shape of the return paths that are displaced in the direction of the coil mesh normal.


## Generate Outputs

The primary purpose of `pyCoilGen` is to calculate the wire path of the coil that produces the desired target field.

### Generate Cylindrical PCB Output

`pyCoilGen` can optionally generate a PCB wire path that is suitable for wrapping around a cylinder.

- `make_cylindrical_pcb` (Type: `bool`, Default: `False`)

  If True, generates a rectangular PCB pattern to wrap around a cylinder.

- `pcb_interconnection_method` (Type: `str`, Default: `'spiral_in_out'`)

  Interconnection method for PCB: 'spiral_in_out' or 'other'.

- `pcb_spiral_end_shift_factor` (Type: `int`, Default: `10`)

  Factor (as a percent) to shift the open ends of the spirals in order to avoid overlaps.

### Generate 3D Wire Path

The application can optionally generate a 3D `.stl` trace by sweeping out a conductor profile along the computed wire path.

- `skip_sweep` (Type: `bool`, Default: `False`)

  If True, skips the generation of a volumetric (3D) coil body.

  The calculated 3D surface is stored in the `layout_surface_mesh` property.

- `cross_sectional_points` (Type: `list of float`, Default: `[0, 0]`)

  This parameter describes the 2D profile of the conductor surface. 

  The default of `[0,0]` instructs `pyCoilGen` to generate a 10-sided circular profile with a radius
  specified by the `conductor_thickness` parameter.

  A custom shape defined by specifying the x/y co-ordinates in metres in a 2xm array of the form `[[x0, x1, x2, x3, ...], [y0, y1, y2, y3, ...]]`.


- `save_stl_flag` (Type: `bool`, Default: `True`)

  If True, saves the swept conductor profile to an `.stl` file.

  If `skip_sweep` is False and `save_stl_flag` is True, the generated result is saved in the output_directory, with a name corresponding 
  to `{project_name}_surface_part{part_ind}_{field_shape_function}.stl`, where `part_ind` is the zero-based index of the winding coil mesh parts.

  The `field_shape_function` is stripped of any `*`, `^`, and `,` symbols.


## Evaluate Results

Once `pyCoilGen` has calculated the wire path, it can also calculate some related values.

### Calculate Inductance

`pyCoilGen` uses [FastHenry2](https://www.fastfieldsolvers.com/software.htm) to calculate the inductance and resistance of the wire path.

- `skip_inductance_calculation` (Type: `bool`, Default: `False`)

  If True, skips calculating the resistance and inductance of the coil solution.

- `conductor_cross_section_width` (Type: `float`, Default: `0.002`)

  Cross-section width of the conductor (for the inductance calculation) in metres.

- `conductor_cross_section_height` (Type: `float`, Default: `0.002`)

  Cross-section height of the conductor (for the inductance calculation) in metres.

- `fasthenry_bin` (Type: `str`, Default: `OS dependent`)

  Specify the location of the `FastHenry2` binary.

  The default directory is determined by the host operating system. 

  On Microsoft Windows, the default installation location is `'C:\Program Files (x86)\FastFieldSolvers\FastHenry2\FastHenry2.exe'`,
  otherwise, it is set to `'/usr/bin/fasthenry'`.

### Evaluate Target Field Errors

- `skip_postprocessing` (Type: `bool`, Default: `False`)

  If True, skips calculating the field errors during post-processing.

<!--

Unused parameters

`min_point_loop_number` (Type: `int`, Default: `20`)
  Minimal required number of points of a single loop; otherwise loops will be removed.


`area_perimeter_deletion_ratio` (Type: `int`, Default: `5`)
  Additional loop removal criteria which relates to the perimeter to surface ratio of the loop.

`max_allowed_angle_within_coil_track` (Type: `int`, Default: `120`)
  Maximum allowed angle of the track of the contours.

### Interconnection Parameters
`interconnection_method` (Type: `str`, Default: `'regular'`)
  Interconnection method: 'regular' or 'spiral' in/out.

`group_interconnection_method` (Type: `str`, Default: `'crossed'`)
  Group interconnection method: 'straight' or 'crossed'.

### Overlap Management Parameters


`track_width_factor` (Type: `float`, Default: `0.5`)
  Track width factor for PCB layout.

-->