I have added some Com_Printf in `R_AddPolygonSurfaces` to debug the values of a bullet polygon on wall, then I took the values and added them natively to this function. Then I made the values configurable by a bunch of cvar's.

	
	/*
	=====================
	R_AddPolygonSurfaces

	Adds all the scene's polys into this view's drawsurf list
	=====================
	*/

	cvar_t *poly_0x = NULL;
	cvar_t *poly_0y = NULL;
	cvar_t *poly_0z = NULL;

	cvar_t *poly_1x = NULL;
	cvar_t *poly_1y = NULL;
	cvar_t *poly_1z = NULL;

	cvar_t *poly_2x = NULL;
	cvar_t *poly_2y = NULL;
	cvar_t *poly_2z = NULL;

	cvar_t *poly_3x = NULL;
	cvar_t *poly_3y = NULL;
	cvar_t *poly_3z = NULL;
	void R_AddPolygonSurfaces( void ) {


		if (poly_0x == NULL) {
			poly_0x = ri.Cvar_Get("poly_0x", "-147", 0);
			poly_0y = ri.Cvar_Get("poly_0y", "153", 0);
			poly_0z = ri.Cvar_Get("poly_0z", "374", 0);

			poly_1x = ri.Cvar_Get("poly_1x", "-146", 0);
			poly_1y = ri.Cvar_Get("poly_1y", "153", 0);
			poly_1z = ri.Cvar_Get("poly_1z", "394", 0);

			poly_2x = ri.Cvar_Get("poly_2x", "-136", 0);
			poly_2y = ri.Cvar_Get("poly_2y", "153", 0);
			poly_2z = ri.Cvar_Get("poly_2z", "391", 0);

			poly_3x = ri.Cvar_Get("poly_3x", "-142", 0);
			poly_3y = ri.Cvar_Get("poly_3y", "153", 0);
			poly_3z = ri.Cvar_Get("poly_3z", "373", 0);

		}

		//Com_Printf("Value of poly_0x=%f\n", poly_0x->value);
		int			i;
		shader_t	*sh;
		srfPoly_t	*poly;
		int		fogMask;

		tr.currentEntityNum = REFENTITYNUM_WORLD;
		tr.shiftedEntityNum = tr.currentEntityNum << QSORT_REFENTITYNUM_SHIFT;
		fogMask = -((tr.refdef.rdflags & RDF_NOFOG) == 0);

		//Com_Printf(" tr.refdef.numPolys=%d\n", tr.refdef.numPolys);
		for ( i = 0, poly = tr.refdef.polys; i < tr.refdef.numPolys ; i++, poly++ ) {
			int j;
			//Com_Printf("poly[%d]->numVerts = %d\n", i, poly->numVerts);
			for (j = 0; j < poly->numVerts; j++) {
				poly->verts[j].xyz[0] *= 1.2;
				poly->verts[j].xyz[1] *= 1.2;
				poly->verts[j].xyz[2] *= 1.2;

			}
			sh = R_GetShaderByHandle( poly->hShader );
			R_AddDrawSurf( ( void * )poly, sh, poly->fogIndex & fogMask, qfalse, qfalse, 0 /*cubeMap*/  );
		}

		int z = tr.refdef.numPolys - 1;
		srfPoly_t *p = tr.refdef.polys + z;
		//Com_Printf("fogIndex=%d hShader=%d numVerts=%d surfaceType=%d\n", p->fogIndex, p->hShader, p->numVerts, p->surfaceType);
		for (i = 0; i < p->numVerts; i++) {
			polyVert_t *v = p->verts + i;
			//Com_Printf("verts[%d] = modulate=%d,%d,%d,%d st=%f,%f xyz=%f,%f,%f \n", i,
			//	v->modulate[0],
			//	v->modulate[1],
			//	v->modulate[2],
			//	v->modulate[3],
			//	v->st[0],
			//	v->st[1],
			//	v->xyz[0],
			//	v->xyz[1],
			//	v->xyz[2]
			//);
		}


		srfPoly_t *clone = tr.refdef.polys + tr.refdef.numPolys;
		clone->fogIndex = 0;
		clone->hShader = 113;
		clone->numVerts = 4;
		clone->surfaceType = 5;
		clone->verts = (polyVert_t *)ri.Hunk_AllocateTempMemory(sizeof(polyVert_t) * 4);

		clone->verts[0].modulate[0] = 255;
		clone->verts[0].modulate[1] = 255;
		clone->verts[0].modulate[2] = 255;
		clone->verts[0].modulate[3] = 255;
		clone->verts[0].st[0] = 0.0;
		clone->verts[0].st[1] = 0.0;
		clone->verts[0].xyz[0] = poly_0x->value;
		clone->verts[0].xyz[1] = poly_0y->value;
		clone->verts[0].xyz[2] = poly_0z->value;

		clone->verts[1].modulate[0] = 255;
		clone->verts[1].modulate[1] = 255;
		clone->verts[1].modulate[2] = 255;
		clone->verts[1].modulate[3] = 255;
		clone->verts[1].st[0] = 0.0;
		clone->verts[1].st[1] = 1.0;
		clone->verts[1].xyz[0] = poly_1x->value;
		clone->verts[1].xyz[1] = poly_1y->value;
		clone->verts[1].xyz[2] = poly_1z->value;

		clone->verts[2].modulate[0] = 255;
		clone->verts[2].modulate[1] = 255;
		clone->verts[2].modulate[2] = 255;
		clone->verts[2].modulate[3] = 255;
		clone->verts[2].st[0] = 1;
		clone->verts[2].st[1] = 0;
		clone->verts[2].xyz[0] = poly_2x->value;
		clone->verts[2].xyz[1] = poly_2y->value;
		clone->verts[2].xyz[2] = poly_2z->value;

		clone->verts[3].modulate[0] = 255;
		clone->verts[3].modulate[1] = 255;
		clone->verts[3].modulate[2] = 255;
		clone->verts[3].modulate[3] = 255;
		clone->verts[3].st[0] = 1.0;
		clone->verts[3].st[1] = 1.0;
		clone->verts[3].xyz[0] = poly_3x->value;
		clone->verts[3].xyz[1] = poly_3y->value;
		clone->verts[3].xyz[2] = poly_3z->value;


		sh = R_GetShaderByHandle(clone->hShader);
		R_AddDrawSurf((void *)clone, sh, clone->fogIndex & fogMask, qfalse, qfalse, 0 /*cubeMap*/);

			
	}

	
Put `poly.cfg` in your `fs_game`:

	// triangle #1 = 0 1 2
	// triangle #2 = 0 2 3

	// z = height
	// y = smaller=more away into depth
	// x = smaller=more right

	// left bottom
	set poly_0x 200
	set poly_0y 200
	set poly_0z 300

	// left top
	set poly_1x 200
	set poly_1y 200
	set poly_1z 400

	// right top
	set poly_2x 50
	set poly_2y 200
	set poly_2z 400

	// right bottom
	set poly_3x 50
	set poly_3y 200
	set poly_3z 300
	
-----	
Limitations
-----

Seems like this function only supports triangles defined by shared vertices (maybe even just 4?). I need a function where I can define myself which vertices make up a triangle.

-----
Questions
-----

How do I allocate the space for needed vertices correctly? ATM `ri.Hunk_AllocateTempMemory` works nice, not sure though if thats correct.
