# final

# 모의 담금질(Simulated Annealing)
:

## 소스코드

### Main
	package finale;

	public class Main {
		public static void main(String[] args) {

        
			SimulatedAnnealing sa = new SimulatedAnnealing(1, 0.95, 100);
			sa.solve(new Problem() {
				@Override
				public double fit(double a,double b,double c) {
					return 1000000*a*a*a+300*((a*b)*(a+b))+3*((b*c)*(b+c))+300*((c*a)*(c+a))+600*a*b*c-100000*a-1000*b-100*c+10000;
				}
				
				@Override
				public boolean isNeighborBetter(double f0, double f1) {
					return f1 > f0;
				}
			}, 0, 31);
			
			System.out.println(sa.hist);
			System.out.println(sa.hist_a);
			System.out.println(sa.hist_b);
			System.out.println(sa.hist_c);
        
		}
	}
### Problem
	package finale;

	public interface Problem {
		double fit(double a,double b,double c);
		boolean isNeighborBetter(double f0,double f1);
	}

### SimulatedAnnealing
	package finale;

	import java.util.ArrayList;
	import java.util.Random;
	
	public class SimulatedAnnealing {
		private double t;   // 초기온도
		private double a;   // 냉각비율
		private int niter;  // 종료조건
		public ArrayList<Double> hist;
		public ArrayList<Double> hist_a;
		public ArrayList<Double> hist_b;
		public ArrayList<Double> hist_c;

		public SimulatedAnnealing(double t, double a, int niter) {
			this.t = t;
			this.a = a;
			this.niter = niter;
			hist = new ArrayList<>();
			hist_a = new ArrayList<>();
			hist_b = new ArrayList<>();
			hist_c = new ArrayList<>();

		}

		public double solve(Problem p, double lower, double upper) {
			Random r = new Random();
			double a0 = r.nextDouble() * (upper - lower) + lower;    // 초기후보해
			double b0 = r.nextDouble() * ((upper-lower)+lower)*50;
			double c0 = r.nextDouble() * ((upper-lower))*100;
			double cost0 = p.fit(a0,b0,c0);                                   // 초기후보해의 적합도
			hist.add(cost0);
			hist_a.add(a0);
			hist_b.add(b0);
			hist_c.add(c0);


			for(int i=0; i<niter; i++) {    // REPEAT
				int kt = (int) Math.round(t * 20);
				for(int j=0; j<kt; j++) {
					double a1 = r.nextDouble() * (upper - lower) + lower;    // 이웃해
					double b1 = r.nextDouble() * ((upper - lower)+lower)*50;  
					double c1 = r.nextDouble() * ((upper - lower)+lower)*100;  
					double cost1 = p.fit(a1,b1,c1);
					if(p.isNeighborBetter(cost0, cost1)) {    // 이웃해가 더 나을 경우
						a0 = a1;
						b0 = b1;
						c0 = c1;
						cost0 = cost1;
						
						hist.add(cost0);
						hist_a.add(a0);
						hist_b.add(b0);
						hist_c.add(c0);
						
					} else {    // 기존해가 더 나을 경우
						double d = cost1 - cost0;
						double p0 = Math.exp(-d/t);
						if(r.nextDouble() < p0) {
							a0 = a1;
							b0 = b1;
							c0 = c1;
							cost0 = cost1;
							
							hist.add(cost0);
							hist_a.add(a0);
							hist_b.add(b0);
							hist_c.add(c0);

						}
					}
				}
				t *= a;
			}
			return a0;
		}
	}

##
