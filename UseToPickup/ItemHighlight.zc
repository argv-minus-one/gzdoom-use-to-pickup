class U2P_ItemHighlight : Thinker {
	enum ETweenElement {
		ITW_OPACITY = 0,
		ITW_TEXT,
		ITW_BRACKET,
		ITW_MAX = ITW_BRACKET
	};
	
	U2P_PlayerState ps;
	Inventory Target;
	float Tween[ITW_MAX+1];
	bool Focused;
	protected float TweenChange[ITW_MAX+1];
	transient U2P_ActorClassInfo ClassInfo;
	protected transient UseToPickup U2P;
	
	// Record this information so that the highlight can fade out, even if the item disappears.
	U2P_ActorAdjustedGeometry TargetGeom;
	String TargetTag;
	
	U2P_ItemHighlight Init(U2P_PlayerState ps, Inventory target, U2P_ActorClassInfo classInfo = null) {
		// Tick after U2P_PlayerState does.
		ChangeStatNum(STAT_USER+1);
		
		self.ps = ps;
		self.Target = target;
		self.ClassInfo = classInfo;
		Focused = false;
		
		for (let i = 0; i <= ITW_MAX; i++)
			Tween[i] = TweenChange[i] = 0;
		
		Reinit();
		return self;
	}
	
	void Reinit() {
		if (!U2P)
			U2P = UseToPickup.Get();
		
		if (!ClassInfo && Target)
			ClassInfo = U2P.ActorClassInfo(Target.GetClass());
		
		if (!TargetTag) {
			if (ClassInfo && ClassInfo.ForceTag)
				TargetTag = StringTable.Localize(ClassInfo.Tag);
			else if (Target) {
				String cn = Target.GetClassName();
				TargetTag = Target.GetTag(
					ClassInfo && ClassInfo.Tag?
					ClassInfo.Tag :
					ClassInfo && ClassInfo.UsePickupMessageAsTag?
					Target.PickupMsg :
					cn
				);
			}
		}
	}
	
	override void Tick() {
		if (!ps || !ps.Player || ps.Player.bot) {
			Destroy();
			return;
		}
		
		float destTween[ITW_MAX+1];
		
		switch (ps.Player.playerstate) {
			case PST_DEAD:
			case PST_REBORN:
				// If player is dead, fade out.
				Target = null;
				break;
			
			case PST_ENTER:
			case PST_GONE:
				// If player isn't present at all, then this object is no longer valid, so bail.
				Destroy();
				return;
			
			// Otherwise, carry on.
		}
		
		if (ps && !ps.Settings.IsEnabled.GetBool())
			// Mod disabled. Fade out all highlights.
			Target = null;
		
		// Compute intended values for the various tweens.
		if (Target && Target.bSpecial) {
			TargetGeom.Update(Target, ClassInfo, bobOffset: true);
			
			destTween[ITW_BRACKET] = 1;
			destTween[ITW_TEXT] = Focused? 1 : 0;
			
			let setting = Focused? ps.Settings.HighlightOpacity : ps.Settings.HighlightOpacityUnfocused;
			destTween[ITW_OPACITY] = setting.GetFloat();
		}
		else {
			if (Tween[ITW_OPACITY] + TweenChange[ITW_OPACITY] <= 0.) {
				// Fully faded out.
				Destroy();
				return;
			}
			else {
				// Start/continue fading out.
				Target = null;
				Focused = false;
				for (let i = 0; i <= ITW_MAX; i++)
					destTween[i] = 0;
			}
		}
		
		// Get how long it's supposed to take to fade in/out.
		double fadeRate[2];
		{
			CVar fadeTimeSetting[2];
			fadeTimeSetting[0] = ps.Settings.FadeInTime;
			fadeTimeSetting[1] = ps.Settings.FadeOutTime;
			
			for (let i = 0; i < 2; i++)
				fadeRate[i] = 1. / (max(fadeTimeSetting[i].GetFloat(), double.epsilon) * TICRATE);
			fadeRate[1] = -fadeRate[1];
		}
		
		for (let i = 0; i <= ITW_MAX; i++) {
			// Apply this tic's change.
			bool changePositive = TweenChange[i] > 0.;
			if (TweenChange[i] && changePositive == (destTween[i] > Tween[i])) {
				if (changePositive)
					Tween[i] = min(destTween[i], Tween[i] + TweenChange[i]);
				else
					Tween[i] = max(destTween[i], Tween[i] + TweenChange[i]);
			}
			
			// Compute next tic's change.
			TweenChange[i] =
				destTween[i] > Tween[i]? fadeRate[0] :
				destTween[i] < Tween[i]? fadeRate[1] :
				0.;
		}
	}
	
	const BackgroundColor_KeyBind = Color(0xffffffe6);
	const BracketSpreadDistance = 0.2;
	
	// Scale the brackets such that they're drawn 1:1 on a screen whose min(width,height) is this many pixels.
	const ScaleBase = 4000.;
	
	ui void Draw(U2P_DrawContext dc) const {
		// Compute tween values for this frame.
		float fracTween[ITW_MAX+1];
		for (let i = 0; i <= ITW_MAX; i++)
			fracTween[i] = clamp(Tween[i] + TweenChange[i], 0., 1.);
		
		if (
			// Don't bother drawing anything if opacity is zero!
			fracTween[ITW_OPACITY] == 0.
		)
			return;
		
		switch (ps.Player.playerstate) {
			case PST_ENTER:
			case PST_GONE:
				// Similarly, if the owning player isn't in the game, don't draw anything.
				return;
		}
		
		Color highlightColor = ps.Settings.HighlightColor.GetInt() | 0xff000000;
		
		// Top left, bottom right
		Vector2 screenCoord[2];
		let halfRad = TargetGeom.Radius * .5;
		
		// Adapted from Pixel Eater's Fill Spectre.
		for (let corner = 0; corner < 4; corner++) {
			// Compute one corner at a time.
			// The corner numbers are: 0 = top left, 1 = top right, 2 = bottom left, 3 = bottom right
			let vert = dc.ViewPos + LevelLocals.Vec3Diff(
				dc.ViewPos,
				TargetGeom.Pos + (
					// `corner & 1` is only true for the top right and bottom right corners.
					sin(dc.Viewer.Angle) * halfRad * (corner & 1? 1. : -1.),
					cos(dc.Viewer.Angle) * halfRad * (corner & 1? -1. : 1.),
					// `corner & 2` is only true for the bottom left and bottom right corners.
					corner & 2? 0 : TargetGeom.Height
				)
			);
			
			let ndc = dc.WorldToClip.multiplyVector3(vert).asVector3();
			
			if (ndc.Z > 1. || ndc.Z < -1.)
				// Out of camera view. Don't draw anything.
				// This is the reason why we store all four coordinates in the screenCoord and then loop over them again: if *any* of the coordinates are out of view, we need to not draw anything!
				return;
			
			let cornerCoord = U2P_GlobalMaths.ndcToViewPort(ndc);
			
			// That projects the corners of the sprite on the screen. Now, make a rectangle from the coordinates.
			if (!corner) {
				// For the first corner, just use that coordinate to start.
				screenCoord[0] = screenCoord[1] = cornerCoord;
			}
			else {
				// From there, stretch out the rectangle to fit the thing.
				screenCoord[0].X = min(screenCoord[0].X, cornerCoord.X);
				screenCoord[0].Y = min(screenCoord[0].Y, cornerCoord.Y);
				screenCoord[1].X = max(screenCoord[1].X, cornerCoord.X);
				screenCoord[1].Y = max(screenCoord[1].Y, cornerCoord.Y);
			}
		}
		
		let texSize = TexMan.GetScaledSize(U2P.BracketTex) * (min(Screen.GetWidth(), Screen.GetHeight()) / ScaleBase);
		
		// Make sure the brackets don't overlap.
		{
			let centerCoord = screenCoord[0] + ((screenCoord[1] - screenCoord[0]) * .5);
			screenCoord[0].X = min(screenCoord[0].X, centerCoord.X - texSize.X);
			screenCoord[0].Y = min(screenCoord[0].Y, centerCoord.Y - texSize.Y);
			screenCoord[1].X = max(screenCoord[1].X, centerCoord.X + texSize.X);
			screenCoord[1].Y = max(screenCoord[1].Y, centerCoord.Y + texSize.Y);
		}
		
		// Spread out the brackets a little further when fading in/out.
		if (fracTween[ITW_BRACKET] < 1.) {
			let cornerAdds = (screenCoord[1] - screenCoord[0]) * .5 * ((1. - fracTween[ITW_BRACKET]) * BracketSpreadDistance);
			let cornerAdd = min(cornerAdds.X, cornerAdds.Y);
			screenCoord[0].X -= cornerAdd;
			screenCoord[1].X += cornerAdd;
			screenCoord[0].Y -= cornerAdd;
			screenCoord[1].Y += cornerAdd;
		}
		
		for (let corner = 0; corner < 4; corner++) {
			Screen.DrawTexture(
				U2P.BracketTex, false,
				screenCoord[corner & 1].X - (corner & 1? texSize.X : 0.),
				// (2 & 2) = 2, but we need 0 or 1. !! gives us that.
				screenCoord[!!(corner & 2)].Y - (corner & 2? texSize.Y : 0),
				DTA_Alpha, fracTween[ITW_OPACITY],
				DTA_AlphaChannel, true,
				DTA_FillColor, highlightColor,
				DTA_FlipX, corner & 1,
				DTA_FlipY, !!(corner & 2),
				DTA_DestWidthF, texSize.X,
				DTA_DestHeightF, texSize.Y
			);
		}
		
		if (fracTween[ITW_TEXT] != 0.) {
			let fontHeight = U2P.MsgFontHeight * CleanYFac;
			
			Vector2 textCoord = (
				screenCoord[0].X,
				screenCoord[1].Y + (fontHeight * .8)
			);
			
			Screen.DrawText(
				U2P.MsgFont, Font.CR_WHITE,
				textCoord.X, textCoord.Y,
				TargetTag,
				DTA_TextLen, fracTween[ITW_TEXT] >= 1.? int.max : int(TargetTag.Length() * fracTween[ITW_TEXT]),
				DTA_Alpha, fracTween[ITW_OPACITY],
				DTA_CleanNoMove, true,
				DTA_Color, highlightColor
			);
			
			textCoord.Y += fontHeight * 1.8;
			
			{
				// Draw the label saying which button to push.
				String keyName;
				{
					int bindingKeys[2];
					[bindingKeys[0], bindingKeys[1]] = Bindings.GetKeysForCommand("+use");
					keyName = KeyBindings.NameKeys(bindingKeys[0], bindingKeys[1]);
				}
				let keyNameWidth = U2P.MsgFont.StringWidth(keyName) * CleanXFac;
				let borderWidth = max(fontHeight >> 2, 1);
				
				Screen.Dim(
					BackgroundColor_KeyBind,
					fracTween[ITW_OPACITY],
					textCoord.X - borderWidth,
					textCoord.Y - borderWidth,
					keyNameWidth + borderWidth + borderWidth,
					fontHeight + borderWidth + borderWidth
				);
				
				Screen.DrawText(
					U2P.MsgFont, Font.CR_WHITE,
					textCoord.X, textCoord.Y,
					keyName,
					DTA_Alpha, fracTween[ITW_OPACITY],
					DTA_CleanNoMove, true,
					DTA_Color, highlightColor
				);
				
				// Advance the draw position for the text.
				textCoord.X += keyNameWidth + borderWidth + borderWidth + U2P.MsgFont.GetCharWidth(20) * 2;
			}
			
			// Draw the “pick up” label.
			Screen.DrawText(
				U2P.MsgFont, Font.CR_WHITE,
				textCoord.X, textCoord.Y,
				U2P.PickUpText,
				DTA_TextLen, fracTween[ITW_TEXT] >= 1.? int.max : int(U2P.PickUpText.Length() * fracTween[ITW_TEXT]),
				DTA_Alpha, fracTween[ITW_OPACITY],
				DTA_CleanNoMove, true,
				DTA_Color, highlightColor
			);
		}
	}
}
