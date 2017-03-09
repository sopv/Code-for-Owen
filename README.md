package org.usfirst.frc.team9495.robot;
 
import edu.wpi.first.wpilibj.DoubleSolenoid;
import edu.wpi.first.wpilibj.IterativeRobot;
import edu.wpi.first.wpilibj.Joystick;
import edu.wpi.first.wpilibj.Talon;
import edu.wpi.first.wpilibj.Timer;
import edu.wpi.first.wpilibj.DoubleSolenoid.Value;
import edu.wpi.first.wpilibj.buttons.JoystickButton;
import edu.wpi.first.wpilibj.command.Command;
import edu.wpi.first.wpilibj.command.Scheduler;
import edu.wpi.first.wpilibj.livewindow.LiveWindow;
import edu.wpi.first.wpilibj.networktables.NetworkTable;
 
import org.usfirst.frc.team9495.robot.commands.ExampleCommand;
import org.usfirst.frc.team9495.robot.subsystems.ExampleSubsystem;
import edu.wpi.first.wpilibj.smartdashboard.SendableChooser;
import edu.wpi.first.wpilibj.smartdashboard.SmartDashboard;
 
/**
 * The VM is configured to automatically run this class, and to call the
 * functions corresponding to each mode, as described in the IterativeRobot
 * documentation. If you change the name of this class or the package after
 * creating this project, you must also update the manifest file in the resource
 * directory.
 */
public class Robot extends IterativeRobot {
 
            	public static final ExampleSubsystem exampleSubsystem = new ExampleSubsystem();
            	public static OI oi;
 
            	NetworkTable table = NetworkTable.getTable("SmartDashboard");
 
            	
	Command autonomousCommand;
	SendableChooser chooser;
	
            	Talon index = new Talon(0);
            	Talon d1 = new Talon(3);
            	Talon flyb = new Talon(2);
            	Talon d3 = new Talon(4);
            	Talon intakem = new Talon(5);
            	Talon d4 = new Talon(6);
            	Talon climbert = new Talon(9);
            	Talon shooter1 = new Talon(1);
            	Talon d6 = new Talon(7);
            	Talon shooter2 = new Talon(8);
            	Talon screw = new Talon(10);
 
            	Joystick left = new Joystick(1);
            	Joystick right = new Joystick(2);
            	Joystick controller = new Joystick(0);
 
            	JoystickButton intakeB = new JoystickButton(controller, 2);
            	JoystickButton reverseI = new JoystickButton(controller, 3);
            	JoystickButton lowerI = new JoystickButton(controller, 1);
            	JoystickButton comp = new JoystickButton(controller, 7);
            	JoystickButton climberb = new JoystickButton(controller, 6);
            	JoystickButton pistonB = new JoystickButton(controller, 5);
            	
            	
            	DoubleSolenoid small = new DoubleSolenoid(1, 4, 5);
            	DoubleSolenoid big = new DoubleSolenoid(1, 2, 3);
            	DoubleSolenoid intakeG = new DoubleSolenoid(1, 0, 1);
 
            	boolean bool = true;
	
	/**
 	* This function is run when the robot is first started up and should be
 	* used for any initialization code.
 	*/
	public void robotInit() {
                            	oi = new OI();
    	chooser = new SendableChooser();
        chooser.addDefault("Default Auto", new ExampleCommand());
//    	chooser.addObject("My Auto", new MyAutoCommand());
        SmartDashboard.putData("Auto mode", chooser);
	}
            	
            	/**
 	* This function is called once each time the robot enters Disabled mode.
 	* You can use it to reset any subsystem information you want to clear when
            	 * the robot is disabled.
 	*/
	public void disabledInit(){
 
	}
            	
            	public void disabledPeriodic() {
                            	Scheduler.getInstance().run();
            	}
 
            	/**
            	 * This autonomous (along with the chooser code above) shows how to select between different autonomous modes
            	 * using the dashboard. The sendable chooser code works with the Java SmartDashboard. If you prefer the LabVIEW
            	 * Dashboard, remove all of the chooser code and uncomment the getString code to get the auto name from the text box
            	 * below the Gyro
            	 *
            	 * You can add additional auto modes by adding additional commands to the chooser code above (like the commented example)
            	 * or additional comparisons to the switch structure below with additional strings & commands.
            	 */
	public void autonomousInit() {
    	autonomousCommand = (Command) chooser.getSelected();
    	
                            	/* String autoSelected = SmartDashboard.getString("Auto Selector", "Default");
                            	switch(autoSelected) {
                            	case "My Auto":
                                            	autonomousCommand = new MyAutoCommand();
                                            	break;
                            	case "Default Auto":
                            	default:
                                            	autonomousCommand = new ExampleCommand();
                                            	break;
                            	} */
	        	
	        	// schedule the autonomous command (example)
    	if (autonomousCommand != null) autonomousCommand.start();
	}
 
	/**
 	* This function is called periodically during autonomous
 	*/
	public void autonomousPeriodic() {
        Scheduler.getInstance().run();
	}
 
	public void teleopInit() {
                            	// This makes sure that the autonomous stops running when
    	// teleop starts running. If you want the autonomous to
    	// continue until interrupted by another command, remove
    	// this line or comment it out.
    	if (autonomousCommand != null) autonomousCommand.cancel();
	}
 
	/**
 	* This function is called periodically during operator control
 	*/
	public void teleopPeriodic() {
        Scheduler.getInstance().run();
    	tankDrive(left.getY(), right.getY());
 
                            	table.putNumber("Trigger", controller.getThrottle());
                            	table.putNumber("Trigger2", controller.getTwist());
 
 
                            	climber();
                            	intake();
                            	shooter();
	}
	
	/**
 	* This function is called periodically during test mode
 	*/
	public void testPeriodic() {
    	LiveWindow.run();
	}
	
 
            	public void tankDrive(double left, double right){
                            	d1.set(-left);
                            	d3.set(-left);
            	            	d4.set(right);
                            	d6.set(right);
            	}
            	
	
	public void intake(){
                            	if(bool){
                                            	if(pistonB.get()){
                                                            	big.set(Value.kForward);
                                                            	Timer.delay(.5);
                                                            	small.set(Value.kForward);
                                                            	bool = false;
                                            	}
                                            	else{
                                                            	if(pistonB.get()){
                                                                            	small.set(Value.kReverse);
                                                                            	Timer.delay(.5);
                                                                            	big.set(Value.kReverse);
                                                                            	bool = true;
                                                                            	
                                                            	}
                                                            	else if(lowerI.get()){
                                                                            	intakeG.set(Value.kReverse);
                                                                            	
                                                                            	
                                                            	}
                                                            	else{
                                                                            	small.set(Value.kForward);
                                                                            	intakeG.set(Value.kForward);
                                                            	}
                                            	}
                                            	
                                            	if(!bool){
                                                            	if(intakeB.get()){
                                                                            	intakem.set(1);
                                                                            	index.set(-.5);
                                                            	}
                                                            	else if(controller.getTwist() >= .8){
                                                                            	intakem.set(.75);
                                                                            	screw.set(-.4);
                                                                            	index.set(.85);
                                                            	}
                                                            	else if(reverseI.get()){
                                                                            	intakem.set(-.5);
                                                            	}
                                                            	else if(lowerI.get()){
                                                                            	intakem.set(1);
                                                                            	index.set(-.5);
                                                            	}
                                                            	else{
                                                                            	intakem.set(0);
                                                                            	index.set(0);
                                                                            	screw.set(0);
                                                            	}
                                            	}else{
                                                                            	intakem.set(0);
                                                                            	index.set(0);
                                                                            	screw.set(0);
                                                                            	intakeG.set(Value.kReverse);
                                            	}
                                            	
                            	}
                            	
                            	
            	}
            	
            	public void shooter(){
                            	if (controller.getThrottle() >= .8){
                                            	shooter1.set(.61);
                                            	shooter2.set(-.61);
                            	}
                            	else{
                                            	shooter1.set(0);
                                            	shooter2.set(0);
 
                            	}
            	}
            	
            	public void climber(){
                            	if (climberb.get()){
                                            	climbert.set(1);
                            	}else{
                                            	climbert.set(0);
                            	}
            	}
 
 
}
