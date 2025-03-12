# OpenFOAM Quadcopter CFD Simulation

This project contains an OpenFOAM CFD simulation setup for analyzing the aerodynamics of a Crazyflie quadcopter. The simulation includes the complete quadcopter geometry with rotating propellers and aims to study the flow field and forces around the drone.

## Project Structure

```
quadcopterCFD/
├── constant/
│   ├── transportProperties    # Fluid properties
│   ├── turbulenceProperties  # Turbulence model settings
│   ├── MRFProperties         # Multiple Reference Frame for rotors
│   └── triSurface/          # STL files for geometry
├── system/
│   ├── blockMeshDict        # Base mesh configuration
│   ├── snappyHexMeshDict    # Mesh refinement settings
│   ├── controlDict          # Simulation control parameters
│   ├── fvSchemes            # Discretization schemes
│   └── fvSolution          # Solution settings
└── [0-9]*                  # Time directories
```

## Prerequisites

- OpenFOAM v2406 or later
- ParaView for visualization
- STL files of the quadcopter components (included in `constant/triSurface/`)

## Setup and Running

1. **Prepare the mesh**:
   ```bash
   ./Allrun
   ```
   This script will:
   - Generate the base mesh using `blockMesh`
   - Extract surface features
   - Create the final mesh using `snappyHexMesh`

2. **Run the simulation**:
   ```bash
   pimpleFoam
   ```

## Mesh Generation Details

The mesh is generated in multiple steps:
1. Base hexahedral mesh using `blockMesh`
2. Surface feature extraction for better refinement
3. Mesh refinement and snapping using `snappyHexMesh`

Key mesh parameters:
- Domain size: (-0.15 -0.15 -0.15) to (0.15 0.15 0.15) meters
- Base cell size: ~5mm
- Refinement levels: 3 levels around the drone body and propellers

## Common Issues and Solutions

### 1. Mesh Generation Issues

#### locationInMesh Error
**Problem**: `snappyHexMesh` fails due to `locationInMesh` point being at (0,0,0)
**Solution**: 
- Modify `system/snappyHexMeshDict`
- Set `locationInMesh` to a point clearly inside the mesh domain but away from geometry
- Example: `locationInMesh (0.05 0.05 0.05);`

#### Mesh Quality Issues
**Problem**: Poor quality mesh elements or mesh generation failure
**Solution**:
- Add `minTriangleTwist` parameter in `meshQualityControls`
- Adjust refinement levels in `snappyHexMeshDict`
- Check STL file quality and orientation

### 2. Simulation Stability Issues

#### Short Simulation Time
**Problem**: Simulation completes too quickly without proper flow development
**Solution**:
- Increase `endTime` in `system/controlDict`
- Adjust `deltaT` for better stability
- Monitor Courant number and adjust accordingly

#### Force Calculation Issues
**Problem**: Incorrect force calculations on propellers
**Solution**:
- Verify MRF zone definitions
- Check force patch definitions
- Ensure proper rotation rates in `MRFProperties`

### 3. Performance Optimization

For better simulation performance:
- Use appropriate turbulence model settings
- Adjust solver tolerances in `fvSolution`
- Consider parallel processing for larger cases

## Visualization

To visualize results in ParaView:
1. Open ParaView
2. File -> Open -> Select the case directory
3. Apply the following filters:
   - Mesh surfaces
   - Velocity vectors
   - Pressure contours

## Contributing

Feel free to contribute to this project by:
1. Forking the repository
2. Creating a feature branch
3. Submitting a pull request

## License

This project is open-source and available under the MIT License.

## Contact

For questions or collaboration, please open an issue on GitHub.
