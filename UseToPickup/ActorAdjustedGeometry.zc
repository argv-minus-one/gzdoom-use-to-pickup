struct U2P_ActorAdjustedGeometry {
	Vector3 Pos, CenterPos;
	double Height, Radius;
	
	void Copy(U2P_ActorAdjustedGeometry other) {
		Pos = other.Pos;
		CenterPos = other.CenterPos;
		Height = other.Height;
		Radius = other.Radius;
	}
	
	play void Update(Actor a, U2P_ActorClassInfo classInfo, bool bobOffset = false) {
		Pos = a.Pos;
		Height = a.Height;
		Radius = a.Radius;
		
		if (!classInfo || (classInfo.UseSpriteDimensions && !(classInfo.OverrideRadius || classInfo.OverrideHeight))) {
			TextureID tex;
			bool flipped;
			Vector2 scale;
			// TODO: Account for sprite rotation?
			[tex, flipped, scale] = a.CurState.GetSpriteTexture(0, scale: a.Scale);
			
			if (tex.IsValid()) {
				let texSize = TexMan.GetScaledSize(tex);
				
				let h = texSize.Y * scale.Y;
				let r = texSize.X * scale.X;
				
				if (h >= 2. && r >= 2.) {
					Height = h;
					Radius = r;
					
					if (!classInfo || !classInfo.OverrideZOffset) {
						let texOffs = TexMan.GetScaledOffset(tex);
						Pos.Z += (texOffs.Y - texSize.Y) * scale.Y;
					}
				}
			}
		}
		
		if (classInfo) {
			if (classInfo.OverrideRadius)
				Radius = classInfo.Radius;
			if (classInfo.OverrideHeight)
				Height = classInfo.Height;
			if (classInfo.OverrideZOffset)
				Pos.Z += classInfo.ZOffset;
		}
		
		if (bobOffset)
			Pos.Z += a.GetBobOffset();
		
		CenterPos = Pos;
		CenterPos.Z += Height / 2;
	}
}
