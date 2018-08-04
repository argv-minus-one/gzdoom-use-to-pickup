class U2P_ItemReachabilityTracer : LineTracer {
	Array<Actor> IgnoreActors;
	
	override ETraceStatus TraceCallback() {
		switch (Results.HitType) {
			case TRACE_HitFloor:
			case TRACE_HitCeiling:
				return TRACE_Stop;
			
			case TRACE_HitActor:
				if (Results.HitActor.bShootable && IgnoreActors.Find(Results.HitActor) == IgnoreActors.Size())
					return TRACE_Stop;
				else
					break;
			
			case TRACE_HitWall:
				if (
					!(Results.HitLine.flags & Line.ML_TWOSIDED) ||
					Results.HitLine.flags & (Line.ML_BLOCKEVERYTHING | Line.ML_BLOCKUSE) ||
					Results.Tier != TIER_Middle
				)
					return TRACE_Stop;
		}
		
		return TRACE_Skip;
	}
}
