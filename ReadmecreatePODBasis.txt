The app. needs to be run on the folder that contains all the case plus the "resultCase" folder, this case it is used to post process the vector in different time. As follow:

> createPODBasis -dict createPODDict

The main dictionary is "createPODDict". Which contains:


cellDensity   (60 36 24);    // dimension x,y,z of the tiles.

minimumCondition    0; // condition for thr SVD calculation.

variables // Variable to be sampled
(
    U
    p
    k
    epsilon
);

cases //Name of the cases considered
(
    {
        name case_3t_shear_1;
        tileZones
        (
            tile1
            tile2
            tile3
        );
    }

    {
        name case_3t_shear_2;
        tileZones
        (
            tile1
            tile2
            tile3
        );
    }
)

resultCaseName    resultCase; // Name of the result case


The name of the tileZone should correspond with the names of the sets in the sampleDict in the system folder on each case. IMPORTANT: "The names in the sampleDict are not checked, so you should only have the tiles definition
in the order you want them and not any other "set" definition for sampling. (This can be improved in the future)

Each tile is sampled using internal tool in OpenFOAM, For this I created a new sample format called "array". An example of sampleDict is found attached:

interpolationScheme cellPoint; //interpolation

sets
(
    tile1 //same name as in the PODCreateBasisDict
    {
        type    array; // sample point in the x,y and z using origin + spanBox and pointsDensity
        axis    x; //not used
        coordinateSystem
        {
            e1      (1 0 0);
            e2      (0 1 0);
            origin  (0 0 18);
        }
        pointsDensity   (60 36 24);
        spanBox         (180 108 72);

    }

    tile2
    {
        type    array;
        axis    x; //not used
        coordinateSystem
        {
            e1      (1 0 0);
            e2      (0 1 0);
            origin  (0 108 18);
        }
        pointsDensity   (60 36 24);
        spanBox         (180 108 72);
    }

    tile3
    {
        type    array;
        axis    x; //not used
        coordinateSystem
        {
            e1      (1 0 0);
            e2      (0 1 0);
            origin  (180 54 18);
        }
        pointsDensity   (60 36 24);
        spanBox         (180 108 72);
    }
);


The application samples the point specified in sampleDict to get the tile. Of course this should have the same dimension pointsDensity   (60 36 24); and the spanBox. This entry can be simplify using wild cards ($) for for dictionaries.


Improvements can be done if large number of cases and large number of points need to be sampled. i.e., you could have an script to create the sampling of point and write down the data into a file. This can be done using the "sample" tool in different processors. Then to create an app to read those sampling and calculate the SVD. Then separating the creation of the tile and the SVD into two sections. This can be done after. Firstly, I want to be sure you are getting the right vector out of the SVD.

To compile this application you'll need to pull from out git 2.1.x
