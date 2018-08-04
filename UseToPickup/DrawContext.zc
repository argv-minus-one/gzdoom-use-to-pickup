struct U2P_DrawContext ui {
	Actor Viewer;
	Vector3 ViewPos, ViewAngles;
	U2P_Matrix WorldToClip;
	double FracTic;
	float ViewerFOV;
	
	void InitFromRenderEvent(RenderEvent evt) {
		Viewer = evt.Camera;
		ViewPos = evt.ViewPos;
		ViewAngles = (evt.ViewAngle, evt.ViewPitch, evt.ViewRoll);
		FracTic = evt.FracTic;
		ViewerFOV = players[consoleplayer].FOV;
		Init();
	}
	
	// This needs to be called *after* ViewPos, ViewAngles, and ViewerFOV are set.
	void Init() {
		// https://forum.zdoom.org/viewtopic.php?f=122&t=61330#p1064117
		WorldToClip = U2P_Matrix.worldToClip(ViewPos, ViewAngles.X, ViewAngles.Y, ViewAngles.Z, ViewerFOV);
	}
}
