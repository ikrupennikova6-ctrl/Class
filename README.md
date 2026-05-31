classDiagram
    class Point3D {
        +double X
        +double Y
        +double Z
        +Point3D(x, y, z)
    }

    class Block {
        +int Id
        +double Length
        +double Width
        +double Height
        +double Mass
        +double Susceptibility
        +double Emission
        +int FixedCompartmentId
        +int AssignedCompartmentId
        +Point3D Position
        +float[] Color
        +Block(id)
        +Clone() Block
    }

    class Compartment {
        +int Id
        +double Xmin, Xmax, Ymin, Ymax, Zmin, Zmax
        +double CenterX, CenterY, CenterZ
        +double SizeX, SizeY, SizeZ
        +Compartment(id)
    }

    class FieldPoint {
        +double X, Y, Z
        +double ExRe, ExIm, EyRe, EyIm, EzRe, EzIm
        +double HxRe, HxIm, HyRe, HyIm, HzRe, HzIm
    }

    class ElectromagneticField {
        -List~FieldPoint~ points
        -double minX, maxX, minY, maxY, minZ, maxZ
        +LoadEField(filePath)
        +LoadHField(filePath)
        +GetElectricFieldAmplitude(x,y,z) double
        +GetMagneticFieldAmplitude(x,y,z) double
        -Interpolate(x,y,z,selector) double
        -FindIndex(sorted, value) int
    }

    class GAIndividual {
        +int[] Chromosome
        +double Fitness
        +double F1, F2, F3
    }

    class GeneticAlgorithm {
        -List~Block~ blocks
        -List~Compartment~ compartments
        -int[,] adjacency
        -double targetCX, targetCY, targetCZ
        -double w1, w2, w3
        -int popSize, generations
        -double crossProb, mutProb
        -Random rand
        +GeneticAlgorithm(blocks, compartments, adj, tx, ty, tz, w1, w2, w3, popSize, generations, crossProb, mutProb)
        +Run() GAIndividual
        -InitPopulation() List~GAIndividual~
        -Evaluate(ind)
        -Tournament(pop) GAIndividual
        -Crossover(p1, p2) GAIndividual
        -Mutate(ind)
    }

    class BeeIndividual {
        +List~Point3D~ Positions
        +double Fitness
        +BeeIndividual(count)
    }

    class BeeAlgorithm {
        -List~Block~ blocks
        -int[,] adjacency
        -Compartment comp
        -double weightDist, weightEMC
        -int popSize, maxIter, limit
        -Random rand
        -ElectromagneticField emField
        -double weightExtEMC
        +BeeAlgorithm(blocks, adj, comp, wDist, wEMC, popSize, maxIter, limit, emField, wExtEMC)
        +Run() List~Point3D~
        -RandomPlacement(ind)
        -IsOverlap(p1,b1,p2,b2) bool
        -Evaluate(ind)
        -LocalSearch(ind, attempts) BeeIndividual
        -Copy(dest, src)
    }

    class StlTriangle {
        +float nx, ny, nz
        +float x1, y1, z1
        +float x2, y2, z2
        +float x3, y3, z3
    }

    class StlReader {
        +ReadAsciiStl(filePath, out minX, maxX, minY, maxY, minZ, maxZ) List~StlTriangle~
        -ReadVertex(line) Point3D
        -CalculateTriangleNormal(v1,v2,v3) Point3D
        -ParseVertexLine(line) float[]
    }

    class MainForm {
        -OpenGLControl glControl
        -List~StlTriangle~ triangles
        -List~Block~ blocks
        -List~Compartment~ compartments
        -int[,] adjacencyMatrix
        -double globalMinX, globalMaxX, ...
        -float rotationX, rotationY, cameraDistance, cameraPanX, cameraPanY
        -Button btnLoadStl, btnAddBlock, btnOptimize, ...
        -NumericUpDown numCompartments, numPopSize, numGenerations, numBeePop, numBeeIter
        -TextBox txtTargetCX, txtTargetCY, txtTargetCZ, txtW1, txtW2, txtW3, txtWDist, txtWEMC, txtExtEMCWeight
        -DataGridView dgvBlocks
        -Label lblStatus, lblGAStatus, lblBeeStatus
        -Panel controlPanel
        -ElectromagneticField emField
        -double finalF1, finalF2, finalF3, finalDist, finalEMC
        +MainForm()
        -CreateOpenGLControl()
        -CreateControlPanel()
        -CreateStatusStrip()
        -BtnLoadStl_Click()
        -BtnAddBlock_Click()
        -BtnOptimize_Click()
        -GenerateRandomBlocks(count)
        -LoadStlFile(filePath)
        -ComputeBoundingBoxAndTransform()
        -CreateCompartments()
        -Form2_KeyDown()
        -GlControl_OpenGLDraw()
        -GlControl_Resized()
        -DrawCuboid(gl, center, lx, ly, lz)
    }

    class Program {
        +Main() void
    }

    Program --> MainForm : creates and runs
    MainForm --> StlReader : uses
    MainForm --> GeneticAlgorithm : uses
    MainForm --> BeeAlgorithm : uses
    MainForm --> ElectromagneticField : has
    MainForm --> Block : contains
    MainForm --> Compartment : contains
    GeneticAlgorithm --> GAIndividual : creates
    GeneticAlgorithm --> Block : uses
    GeneticAlgorithm --> Compartment : uses
    BeeAlgorithm --> BeeIndividual : creates
    BeeAlgorithm --> Block : uses
    BeeAlgorithm --> Compartment : uses
    BeeAlgorithm --> ElectromagneticField : uses
    Block --> Point3D : has position
    StlTriangle --> Point3D : uses vertices
    StlReader --> StlTriangle : creates
    ElectromagneticField --> FieldPoint : contains
